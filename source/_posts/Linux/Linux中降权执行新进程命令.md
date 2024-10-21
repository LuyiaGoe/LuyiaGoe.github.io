---
title: Linux 中降权执行新进程命令
layout: post
author: LuyiaGoe
categories: Linux
cover: false
coverImg: ''
top: true
mathjax: false
tags:
  - Linux
excerpt: post
summary: ''
date: 2024-10-21 09:48:50
---

## 背景介绍
在 Linux 中，有时需要编写 Root 权限的 Service Systemd 服务，但是并不希望将服务派生的其他进程或命令执行也以 Root 权限执行，此时就需要进行降权

## 降权
如果已知普通权限的用户名和用户组，可以通过 `getpwnam` 和 `getgrnam` 获取对应的 id 信息

```cpp
#include <pwd.h>

......
struct passwd *user_info = getpwnam("user"); // 替换为实际的用户名
struct group *group_info = getgrnam("group"); // 替换为实际的组名

uid_t user_uid = user_info->pw_uid;
gid_t user_gid = group_info->gr_gid;
......
```

如果未知，则可以用如下代码获取当前活跃用户的uid和gid：
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <pwd.h>
#include <utmp.h>

void get_active_user_and_group_ids()
{
    struct utmp utbuf;
    int fd;

    fd = open(_PATH_UTMP, O_RDONLY);
    if (fd == -1)
    {
        perror("open");
        return;
    }

    while (read(fd, &utbuf, sizeof(utbuf)) == sizeof(utbuf))
    {
        if (utbuf.ut_type == USER_PROCESS)
        {
            struct passwd *pw = getpwnam(utbuf.ut_user);
            if (pw && pw->pw_uid > 999 && pw->pw_uid < 65535)
            { // 普通用户的UID大于999且小于65535
                printf("活跃的用户ID: %d, 用户名: %s, GID: %d\n", pw->pw_uid, utbuf.ut_user, pw->pw_gid);
            }
        }
    }

    close(fd);
}
```

获取到 ids 后，即可通过 `fork` 出孙进程，并用 `setgid` 和 `setuid` 来切换到这些用户

## fork
为了防止新命令的子进程成为僵尸进程，可以通过两次 fork 来创建孙进程，并通过 `waitpid` 等待子进程的退出，让孙进程成为孤儿进程被 `init` 或 `systemd` 进程收养，代码如下：

```cpp
int execCmd(const char *cmd)
{
    // 获取 ids
    uid_t user_uid = ...;
    gid_t user_gid = ...;


    pid_t pid = fork();

    // fork 出子进程
    if (pid < 0)
    {
        // fork 失败
        std::cerr << "子进程 Fork 失败\n";
        return 1;
    }
    else if (pid == 0)
    {
        std::cout << "子进程打印\n";
        // 子进程 fork
        pid_t child_pid = fork();
        if (child_pid < 0)
        {
            std::cerr << "孙进程 fork 失败\n";
            return 1;
        }
        else if (child_pid > 0)
        {
            std::cout << "子进程退出\n";
            exit(0);
        }
        else
        {
            std::cout << "孙进程打印" << getppid() << getpid() << "\n";
            sleep(2); // 延时, 等待父进程退出
            std::cout << "孙进程打印" << getppid() << getpid() << "\n";

            // 降权
            if (setgid(user_uid) != 0 || setuid(user_gid) != 0)
            {
                std::cerr << "Failed to drop privileges\n";
                return 1;
            }

            char *const argv[] = {(char *const)cmd, nullptr};
            execvp(argv[0], argv);

            // 如果 execvp 返回，说明执行失败
            std::cerr << "Exec failed\n";
        }
        return 1;
    }
    else
    {
        // 父进程
        std::cout << "父进程打印\n";
        // 等待子进程结束，防止其成为僵尸进程
        waitpid(pid, nullptr, 0);
    }

    return 0;
}
```