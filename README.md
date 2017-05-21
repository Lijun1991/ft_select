# ft_select

 Get and set window size
       Window sizes are kept in the kernel, but not used by the kernel
       (except in the case of virtual consoles, where the kernel will update
       the window size when the size of the virtual console changes, for
       example, by loading a new font).

       The following constants and structure are defined in <sys/ioctl.h>.

       TIOCGWINSZ     struct winsize *argp
              Get window size.

       TIOCSWINSZ     const struct winsize *argp
              Set window size.

       The struct used by these ioctls is defined as

           struct winsize {
               unsigned short ws_row;
               unsigned short ws_col;
               unsigned short ws_xpixel;   /* unused */
               unsigned short ws_ypixel;   /* unused */
           };

       When the window size changes, a SIGWINCH signal is sent to the
       foreground process group.
