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


tputs(tgetstr("cl", NULL), 1, ft_writer)

`cl'
String of commands to clear the entire screen and position the cursor at the upper left corner.

`us'
String of commands to turn on underline mode or to output a magic cookie to start underlining.

`ue'
String of commands to turn off underline mode or to output a magic cookie to stop underlining.

`so'
String to enter standout mode.

`se'
String of commands to leave standout mode.

`ve'
String of commands to return the cursor to normal.

`vi'
String of commands to make the cursor invisible.

`ti'
String of commands to put the terminal into whatever special modes are needed or appropriate for programs that move the cursor nonsequentially around the screen. Programs that use termcap to do full-screen display should output this string when they start up.

`te'
String of commands to undo what is done by the `ti' string. Programs that output the `ti' string on entry should output this string when they exit

`cr'
String to move cursor sideways to left margin.

`ho'
String of commands to move the cursor to the upper left corner of the screen (this position is called the home position). In terminals where the upper left corner of the screen is not the same as the beginning of display memory, this command must go to the upper left corner of the screen, not the beginning of display memory. Every display terminal supports this capability, and many application programs refuse to operate if the `ho' capability is missing.

`do'
String of commands to move the cursor vertically down one line. The effect of sending this string when on the bottom line is undefined; programs should never use it that way. Some programs do use `do' to scroll up one line if used at the bottom line, if `sf' is not defined but `sr' is. This is only to compensate for certain old, incorrect terminal descriptions. (In principle this might actually lead to incorrect behavior on other terminals, but that seems to happen rarely if ever.) But the proper solution is that the terminal description should define `sf' as well as `do' if the command is suitable for scrolling. The original idea was that this string would not contain a newline character and therefore could be used without disabling the kernel's usual habit of converting of newline into a carriage-return newline sequence. But many terminal descriptions do use newline in the `do' string, so this is not possible; a program which sends the `do' string must disable output conversion in the kernel (see section Initialization for Use of Termcap).

`up'
String of commands to move the cursor vertically up one line. The effect of sending this string when on the top line is undefined; programs should never use it that way.

`nd'
String of commands to move the cursor right one column. The effect is undefined if the cursor is at the right margin; do not use this command there, not even if `am' is present




 The termios structure
       Many of the functions described here have a termios_p argument that
       is a pointer to a termios structure.  This structure contains at
       least the following members:

           tcflag_t c_iflag;      /* input modes */
           tcflag_t c_oflag;      /* output modes */
           tcflag_t c_cflag;      /* control modes */
           tcflag_t c_lflag;      /* local modes */
           cc_t     c_cc[NCCS];   /* special characters */
           
 c_lflag flag constants:
 
  ICANON Enable canonical mode (described below).
  ECHO   Echo input characters.
