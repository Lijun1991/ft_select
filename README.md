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
  
 The c_cc array defines the terminal special characters.  The symbolic
       indices (initial values) and meaning are:
   VMIN   Minimum number of characters for noncanonical read (MIN).
   VTIME  Timeout in deciseconds for noncanonical read (TIME).
   
  
  Canonical and noncanonical mode
       The setting of the ICANON canon flag in c_lflag determines whether
       the terminal is operating in canonical mode (ICANON set) or
       noncanonical mode (ICANON unset).  By default, ICANON is set.

       In canonical mode:

       * Input is made available line by line.  An input line is available
         when one of the line delimiters is typed (NL, EOL, EOL2; or EOF at
         the start of line).  Except in the case of EOF, the line delimiter
         is included in the buffer returned by read(2).

       * Line editing is enabled (ERASE, KILL; and if the IEXTEN flag is
         set: WERASE, REPRINT, LNEXT).  A read(2) returns at most one line
         of input; if the read(2) requested fewer bytes than are available
         in the current line of input, then only as many bytes as requested
         are read, and the remaining characters will be available for a
         future read(2).

       * The maximum line length is 4096 chars (including the terminating
         newline character); lines longer than 4096 chars are truncated.
         After 4095 characters, input processing (e.g., ISIG and ECHO*
         processing) continues, but any input data after 4095 characters up
         to (but not including) any terminating newline is discarded.  This
         ensures that the terminal can always receive more input until at
         least one line can be read.

       In noncanonical mode input is available immediately (without the user
       having to type a line-delimiter character), no input processing is
       performed, and line editing is disabled.  The read buffer will only
       accept 4095 chars; this provides the necessary space for a newline
       char if the input mode is switched to canonical.  The settings of MIN
       (c_cc[VMIN]) and TIME (c_cc[VTIME]) determine the circumstances in
       which a read(2) completes; there are four distinct cases:

       MIN == 0, TIME == 0 (polling read)
              If data is available, read(2) returns immediately, with the
              lesser of the number of bytes available, or the number of
              bytes requested.  If no data is available, read(2) returns 0.

       MIN > 0, TIME == 0 (blocking read)
              read(2) blocks until MIN bytes are available, and returns up
              to the number of bytes requested.

       MIN == 0, TIME > 0 (read with timeout)
              TIME specifies the limit for a timer in tenths of a second.
              The timer is started when read(2) is called.  read(2) returns
              either when at least one byte of data is available, or when
              the timer expires.  If the timer expires without any input
              becoming available, read(2) returns 0.  If data is already
              available at the time of the call to read(2), the call behaves
              as though the data was received immediately after the call.

       MIN > 0, TIME > 0 (read with interbyte timeout)
              TIME specifies the limit for a timer in tenths of a second.
              Once an initial byte of input becomes available, the timer is
              restarted after each further byte is received.  read(2)
              returns when any of the following conditions is met:

              *  MIN bytes have been received.

              *  The interbyte timer expires.

              *  The number of bytes requested by read(2) has been received.
                 (POSIX does not specify this termination condition, and on
                 some other implementations read(2) does not return in this
                 case.)

              Because the timer is started only after the initial byte
              becomes available, at least one byte will be read.  If data is
              already available at the time of the call to read(2), the call
              behaves as though the data was received immediately after the
              call.

       POSIX does not specify whether the setting of the O_NONBLOCK file
       status flag takes precedence over the MIN and TIME settings.  If
       O_NONBLOCK is set, a read(2) in noncanonical mode may return
       immediately, regardless of the setting of MIN or TIME.  Furthermore,
       if no data is available, POSIX permits a read(2) in noncanonical mode
       to return either 0, or -1 with errno set to EAGAIN.
