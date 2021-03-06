
                              ToneLoc v0.98

                               User Manual

                                   by

                        Minor Threat & Mucho Maas

     ToneLoc is short for Tone Locator, and is a bit of a wild thing.
What it does is simple: it dials numbers, looking for some kind of tone.
It can also look for carriers like an ordinary wardialer.

It is useful for:

   1. Finding PBX's.
   2. Finding loops or milliwatt test numbers.
   3. Finding dial-up long distance carriers.
   4. Finding any number that gives a constant tone, or something
      that your modem will recognize as one.
   5. Finding carriers (other modems)
   6. Hacking PBX's.

     Before you even start using ToneLoc, PLEASE PLEASE take the time to
print out and read the docs.  Well, you don't have to print them out,
but at LEAST read them.  ToneLoc is extremely flexible and can be
configured to work on almost any modem under almost any environment.  It
has been extensively tested on everything from generic 1200's to USR
16.8k duals.  Unfortunately, flexibility has its price.  You can
probably get just about anything short of an acoustic coupler to work
with ToneLoc, but you'll need to spend some time configuring it
properly. Trust us, reading the docs now will alert you to many useful
features, and save you headaches later.  To sum it up, ToneLoc rocks and
if you don't read the docs, you're a LAMER!


Here are the command line options for ToneLoc:

ToneLoc  [DataFile]  /M:[Mask] /R:[Range] /D:[ExRange] /X:[ExMask]
         /C:[Config] /S:[StartTime] /E:[EndTime] /H:[Hours] /T[-] /K[-]

    You can use ":" or "-" as a delimiter. If you don't use ":" or "-",
ToneLoc will assume there is no delimiter. Example:  ToneLoc [DataFile]
/M[Mask] ...

     When you run ToneLoc you need to give it at least one command line
parameter.  The only required parameter is a data filename; the rest are
optional.  The optional parameters can come in any order.  If you only
provide a filename, the filename is also used as the mask.  A mask tells
ToneLoc what numbers to dial.  A mask will look something like this:
555-1XXX.  The X's are replaced by ToneLoc with random numbers.  It will
never dial the same random number twice in the same mask.  If you exit
before the mask has been exhausted, ToneLoc will save the array of
numbers dialed and their results in the data file.  You should never
have more than 4 X's in a mask.  ToneLoc will run, but since ToneLoc
uses integer variables, the numbers will be all screwed up, since 5 X's
would have 100,000 possible numbers which is more than 32,768 (integer)
and 65,536 (word).  If you have no idea what we're talking about, just
trust us and don't put 5 X's in the mask.

    The next command line parameter is the Mask (/M).  If you use this,
your data filename can be anything you want, and the mask will be taken
from the string following /M.

     The next parameter is the range to dial (/R).  This makes it easier
to specify a range of numbers without having to exclude numbers.  Say
you want to dial from 835-1000 to 835-2000, you would run:
TONELOC 835-XXXX /R:1000-2000.

     The next parameter is the range to NOT dial (/D).  Say you want to
dial 345-xxxx, but you know that 345-9000 - 345-9999 are all payphones.
Run:  TONELOC 345-XXXX /D:9000-9999.  ToneLoc would dial everything
except the 9000-9999 range.

     Another way to accomplish the same thing would be to use an Exclude
mask.  (/X)  This is a mask of numbers NOT to dial.  To dial the entire
345 prefix, EXCEPT the 5000-5999 range, you could run:

TONELOC 345-XXXX /X:5XXX

Notice that is "/X:5XXX" and not "/X:345-XXXX".  The Exclude mask must be
a subset of the original mask.  You can specify up to 10 exclude masks.

     Excluded numbers (from masks or ranges) are only excluded for the
current run of ToneLoc - the flagging is not permanent.  Between your
dial masks and ranges you should be able to obtain a good degree of
specificity in your scan.

     The next command line parameter (/C) is which configuration file to
use (.CFG).  This file contains all of the configuration data for ToneLoc,
such as which COM port to use, the baud rate, window colors, dial string,
etc.  See the configuration file for details.

     The next parameter is the starting time (/S).  ToneLoc will wait
until this time to begin the dial scan.  You can use either standard
time notation (5:30p) or military time (17:30) for any time parameter.
You can hit any key to start early.

     The next parameter is the ending time (/E).  When this time is
reached ToneLoc will end the current scan.

     The next parameter is a useful shortcut (/H).  It specifies an end
time at a certain number of hours and minutes past the start time.  If
you specify a start time and a number of hours (/S:10:00p /H:5:30), the
end time will be the start time plus the number of hours desired (3:30
AM). If you specify both an end time and a number of hours, the number
of hours will take precedence.

     The next few parameters are overrides for the scan type (/T, /K,
/T-, /K-).  This is usually set in the config file, but this parameter
overrides it.  To scan for tones you'd use /T.  To scan for everything
except tones use /T-.  To scan for carriers you'd use /K, to scan for
everything except carriers use /K-.  The inverted scan modes are useful
for hacking a PBX; see below on hacking PBX's.

     The datafile should be 10016 bytes at all times.  If you have data
files from previous versions of ToneLoc, there is a utility included with
ToneLoc called "TCONVERT" that will bring your data files up-to-date.
There can be as many data files in the directory as you want.  Don't
forget to SAVE your data files, they don't take too much space, and they
are great with ToneMap (see below).

     Here are a few example command lines:

 ToneLoc 346-XXXX           - Dial 346-0000 to 346-9999 using the
                              default configuration file, saving
                              responses to the data file 346-XXXX.DAT.

 ToneLoc 950-5XXX /C:NINE5  - Dial 1000 numbers, from 950-5000 to 950-5999
                              (randomly), and use the configuration file
                              NINE5.CFG. This configuration file might
                              skip rings and have a short wait. This
                              could be used for dialups.

 ToneLoc 474-9XXX /X:1XX    - Dial 1000 numbers, from 474-9000 to 474-9999
                              (randomly), using the default configuration
                              file TONELOC.CFG, but exclude 474-9100 to
                              474-9199.  Also see next example.

 ToneLoc 474-XXXX /R:9000-9999 /X:91XX
                            - Same as above, but easier to understand.
                              This method is better for another reason:
                              If you scan 9000-9999 now, and later decide
                              to scan the rest of the prefix, this method
                              would keep the whole scan in one data file,
                              rather than having 474-9XXX.DAT  and
                              474-XXXX.DAT.

 ToneLoc 474-XXXX /R:9000-9999 /D:9100-9199
                             - Another version of the above.

 ToneLoc 836-99XX /C:LOOP /S:21:30
                            - Dial from 836-9900 to 836-9999 (100 numbers)
                              using the config file LOOP.CFG, but waiting
                              until 9:30 PM to begin dialing.

 ToneLoc TEST /M555-1XXX /H:5:00 /x:3XX /x:1XX
                             - Dial the numbers from 555-1000 to 555-1999
                               for five hours maximum, saving the dialed
                               numbers to TEST.DAT, and excluding the
                               ranges 1300-1399 and 1100-1199.

 ToneLoc 677-8xxx /E:8:30a   - Dial the numbers 677-8000 to 677-8999
                               until 8:30 AM, saving the dialed numbers
                               to 677-8XXX.DAT.

     The optional parameters can come in any order, but the name of the
datafile MUST be the first parameter.  If there is no mask specified, the
data file name is used as the mask.

     We hope you are impressed by the way the screen looks while dialing.
The screen is split up into 3 major windows.  The first window, called the
Activity Log, takes up the entire left half of the screen.  It tells you
what is going on.  If LOGGING is ON, everything that appears here also
goes to the log file.  The following messages may appear in the message
log:

     22:54:09 ¯
          This is written at the beginning of each run.  It makes it
          easier for you to separate ToneLoc runs in the log file.

     22:53:53 ToneLoc started on 31-Jan-93
          This is self explanatory.

     22:53:53 Data file:   403-XXXX.DAT
          This shows which file ToneLoc is using to store the dialed
          numbers.

     22:53:53 Config file: TONELOC.CFG
          This shows which file ToneLoc has loaded the configuration
          information from.  TONELOC.CFG is the default configuration
          file.

     22:53:53 Log file:    TONE.LOG
          This shows which file ToneLoc is logging the scan to.  This
          file name is set in the configuration file and can be changed
          there.

     22:53:53 Mask used:   403-XXXX
          This tells what mask you used for the current run.

     22:53:53 Exclude mask 1:  8XXX
          Shows which numbers you AREN'T dialing in the current run.

     22:53:53 Initializing modem ...
          ToneLoc is trying to initialize the modem.  It will either
          give a "Done" message or a "Failed" Message.  Toneloc will
          try 3 times to initialize the modem.

     22:53:53 Waiting until 09:30:00
          ToneLoc is waiting until 9:30 AM to start the current scan.
          You can hit any key to start early.

     23:30:44 474-5294 - Timeout (1)
          This means the number was dialed, it rang ONCE (notice the '(1)' ),
          and then it timed out without finding anything.

     23:30:56 474-5335 - Timeout (3)
          This means the number was dialed, and nothing was found during
          the WaitDelay. The (3) indicates there were three rings.

     23:31:00 474-5978 - No Dialtone #1
          This means when ToneLoc tried to dial, there was no dial tone
          found (your dialtone).  When this happens, ToneLoc tries the
          same number again, until it has tried the number of times by
          specified by NoToneAbort in the config file.

     23:39:02 474-5685 - Busy
          This means the number dialed was busy.

     00:24:26 474-5989 - ** TONE **
          Holy Shit!  You found a tone.  It is probably either a loop,
          PBX, or dial-up LD carrier.  Now its your job to hack it out
          and use it!

     09:14:34 353-0911 - * CARRIER *
          Even better!  You found a carrier.  If you're lucky, it's
          your DATAKIT dialup.  Otherwise, it could be a BellCore
          unix!  Of course it could be a do-nothing carrier.  Those suck.

     00:24:26 474-5489 - Voice   (1)
          This means your modem detected a voice answer.  Good modems
          like the USR HST/DS can detect voice.  X5 or X6 in your init
          string will enable this on a HST/DS.  CAUTION: the "VOICE"
          response can be triggered by some dialtones, so you may want
          to disable this if you are scanning for tones. See below.

     06:45:43 Ringout (3)
          This means MaxRings (in this case 3) was reached and the dial
          was aborted. See below for a discussion of rings.

     15:11:23 474-5555 - * Blacklisted #5 *
          This means the number was found in the BlackList file
          (the 5th entry), so it was not dialed.  This is highly
          recommended for areas with Caller ID and ex-girlfriends.

     00:45:01 Autosaving
          This means Toneloc is backing up the .DAT file after the
          interval set in the config file.

     04:53:12 Stopping at 10:00:21
          ToneLoc has reached the stop time specified after /E and is
          exiting the current scan.

     03:00:32 All 10000 codes exhausted
          Damn, you dialed every possible number!  3 X's means 1000
          numbers are possible.  4 X's means 10,000 numbers are
          possible, etc.  Like this: 10^X, where X is the number of X's
          in the mask.  Math sucks.

Other messages are in response to input:

     00:25:31 474-5629 - Speaker ON
          By hitting S you can toggle the speaker on and off DURING a
          scan.  ToneLoc will beep high (ON) or low (OFF) depending on
          the status of the speaker.  ToneLoc waits until it is finished
          with the current dial to toggle the speaker.

     00:28:45 474-9091 - Volume set to 3
          By hitting a number 0-9 you can set the volume level with the
          commands defined in the Config file. You can also use them for
          customized commands.

     00:25:59 474-5985 - * Noted *
          You can hit N to make a note in the log next to this number.
          Aborts current number.  Use it when you find something
          interesting like a drunk cowboy yelling at you through the
          phone.  Other note keys are:
             C - Carrier
             F - Fax
             G - Girl
             K - Custom note (you can type a note yourself)
             V - VMB
             Y - Yelling asshole

     00:27:23 474-5239 - Jumped to DOS
          Hit J to shell to DOS.  Just type EXIT to return.  This will
          abort the current number being dialed, but ToneLoc will redial
          it after you return from DOS.  Be careful to "exit" and not
          to just re-run Toneloc.

     00:27:45 474-5722 - Redialing
          Hit R to redial the current number.  Useful if a number doesn't
          "take" or you want to fuck with that drunk cowboy who answered
          last time.

     00:30:45 474-5123 - Escaped
     03:30:45 Dials/hour : 225
     00:30:46 ToneLoc Exiting ...
          Hitting escape will abort the current number and exit the
          program. ToneLoc writes the average number of dials per hour
          to the log file.

     00:28:12 474-5756 - Aborted
          Hitting the Spacebar will abort the current number.

     00:45:23 454-5365 - Paused
          Pressing P will stop the current dial and wait for another
          keypress before continuing.  Good in case you want to use
          the phone for a sec.

A few keys don't have screen responses:

  X : Adds 5 seconds to the WaitDelay time for this dial only. Can be
      used repeatedly on the same dial.

     Ok, on to the next window.  The top-right corner of your screen is
the modem window.  Everything that is returned from your modem is shown
here.  This isn't very useful, except maybe for debugging, but it looks
neat.
     The last window is in the bottom-right part of the screen.  Its
called the Statistics window.  It shows a bunch of cool stuff like....

     þ The time you began scanning.
     þ The current time.
     þ The maximum number of possible numbers,
       based on your mask and negative mask.
     þ The number of numbers already dialed.
     þ Number of responses for CD (carriers), Tone, Voice, Busy, & Ringout.
     þ The average number of dials per hour.
     þ ETA - Estimated Time to Arrival (or completion).
       This is the number of hours and minutes left in the scan, based
       on your current dials per hour and numbers left.
     þ The number of rings so far in the current dial.
     þ Last 5 tones or carriers found.

     You'll also notice (you better!) the meter at the bottom right.
Pretty cool huh?  It just shows the progress of the current call.  This
is a graphic representation of the elapsed wait time as set in the config
file.  If you can't stand to look at a still screen, set a fancy meter
wipe option in the config file.

The Black List File:
--------------------

     This is a file of up to 1000 numbers that ToneLoc should never dial.
Put your own numbers here, your friends numbers, the police department,
fire department, etc.  Each number should be on its own line exactly as
ToneLoc will dial them. For example the entry "555-1212" will only
blacklist the number "555-1212", not "1-555-1212" or "5551212". If
ToneLoc comes up with one of these numbers as a candidate for a dial
attempt, it will skip it and move on to the next number.  Anything after
a semicolon (;) is ignored, so you can comment this file.

Rings And The X Parameter:
--------------------------

     This discussion refers in particular to newer USRobotics modems. If
you are using another brand of modem you'll probably have to sort
through the details yourself.

     This can get a little confusing so a little detail is in order.
There are several ways to deal with the RINGING message that your modem
can generate.  The simplest is to simply disable it with the X4 command
in your modem init string.  With X4, RINGING and VOICE will be supressed
as responses.  This is simple enough, but you won't get much diagnostic
detail in your logs or .DAT files, and your scan will take longer
because more of the calls will go all the way until timeout instead of
aborting earlier because of a Ringout or Voice response.  You can enable
these messages with the X6 flag, which will respond with VOICE and
RINGING when it is detected.  Unfortunately, the USR is no AppleCat, and
VOICE can give a false response when you are looking for dialtones.  Of
particular importance, the high pitched tone (2600hz aka wink-start)
which precedes many PBX's initial dialtone will cause a VOICE response.

     X7 supresses the VOICE response, but leaves the RINGING response.
In our experience RINGING is seldom a false response, and any potential
VOICE responses will show up as BUSY's.  If you decide to use X7, you'll
need to adjust the MaxRings parameter in your config file.  Experiment a
little bit to decide how to set it.  If you set it to 0, the number of
rings will be recorded, but ToneLoc will never abort because of rings.

     If you are using a USRobotics modem to scan for carriers, however,
you should use the X6 command since the modem will never give a false
response when looking for carriers.  Your scan will go faster, and your
.DAT file will be more detailed.

After the Scan:
---------------

    Well now that I have some dial tones, what the fuck do I do with
them?  First, figure out what kind of a number it is.

    PBX's usually have a 3-8 digit code, but they can be longer, or they
can have NO code.  If you enter the correct code, you will hear a second
dial tone.  Otherwise you will probably get a reorder (fast busy), busy,
a hangup, or ringing.  Sometimes it will ring the PBX attendant (the
operator - ugh).  But ringing the attendant is a good way to find out
who owns the PBX.  Once you get the second dialtone, dial 9+ACN (sometimes
X+ACN, where X is often 7 or 8, and less frequently other digits) to make
a long distance call.  (NOTE: ACN = Area Code & Number) Some PBX's have
no code, you just need to dial 9.  Sometimes the code will follow the
number in the format 9+ACN+Code. Sometimes you'll need to dial 1 first.
Many will also call international.  Experiment.  See below on hacking them.

    It might also be a long-distance extender dial-up.  You'll find many
of them in the prefix 950-xxxx.  Sometimes it is easy to hack a code, but
please be careful!  They are easy to get busted on.  MCI people are dicks.
They get off on busting people, and announcing it to the world.  Sprint
doesn't fuck around either, they'll bust you, but they like to keep it
quiet.  And the little guys are getting smarter too.  Consult with
local phreaks before experimenting with an unfamiliar extender.

   Here's a tip.  If you scan 950's you'll find most will give either a
result of Voice, Ring, or Busy. A few will be Tones, but also a few will
be Timeouts.  Investigate these - you may find something interesting,
like a voice-prompted dialup or a modem carrier.

    You may also find "Phantoms".  In Mucho's area there are several MCI
dialup ports that are no longer in use since the full implementation of
Equal Access.  Hack all day, you won't find a code.  Try and figure out
what you are hacking before you waste time on a dead end.

    Now, for an explanation of loops.  We'll tell you what we know about
them, which ain't a whole lot.  Loops are a pair of phone numbers,
usually consecutive, like 836-9998 and 836-9999.  They are used by the
phone company for testing.  What good do loops do us?  Well, they are
cool in a few ways.  Here is a simple use of loops.  Each loop has two
ends, a 'high' end, and a 'low' end.  One end gives a (usually) constant,
loud tone when it is called. The other end is silent.  Loops don't usually
ring either.  When BOTH ends are called, the people that called each end
can talk through the loop.  Some loops are voice filtered and won't pass
anything but a constant tone; these aren't much use to you.  Here's what
you can use working loops for:  billing phone calls!  First, call the end
that gives the loud tone.  Then if the operator or someone calls the other
end, the tone will go quiet.  Act like the phone just rang and you answered
it ... say "Hello", "Allo", "Chow", "Yo", or what the fuck ever.  The
operator thinks that she just called you, and that's it!  Now the phone
bill will go to the loop, and your local RBOC will get the bill!  Use this
technique in moderation, or the loop may go down.  Loops are probably most
useful when you want to talk to someone to whom you don't want to give
your phone number.

     As for carriers.. well, we would hope you know what to do with a
carrier by now.  But if you don't, a good place to start is The Mentor's
Guide to Hacking (Phrack, I forget which issue).

ToneMap - Something New
------------------------

    When we first wrote and ran ToneMap, we were amazed by what we saw.
ToneMap reads a ToneLoc .DAT file, and displays the data visually on the
screen.  Big deal, right?  Actually, it can be useful.  We saw more than
just scattered colors.  We saw definate patterns within the prefixes we
scanned.  Hopefully you took the time to print this doc file out, because
We are going to go over one of the example .DAT files with you.

    Run ToneMap like this: "TONEMAP 555-XXXX" and press Enter.  (You'll
need VGA).  You should see a square of colors that takes about 2/3 of the
screen, and a key to the colors on the right.  Each square represents a
response type of a single phone number in the prefix.  It starts at the top
left (0000) and works down and to the right (9999).  Each vertical column
is 100 numbers.

    Here's an explanation of the colors:

    BLACK        = Undialed (Not yet dialed by ToneLoc)
    GREY         = Timeout (Lighter = more rings before timeout)
    ORANGE/RED   = Busy number.
    DARK BLUE    = Blacklisted number.
    DARK GREEN   = RingOut. (Rang too many times)
    LIGHT GREEN  = Tone
    LIGHT YELLOW = Carrier
    CYAN         = NOTED Number ('N' was pressed)
    DARK RED     = Aborted (spacebar pressed)

     There are other colors too, as you can see in the key, but the ones
above are the important ones.  Unless you're colorblind, you have
probably already noticed a pattern to this prefix.  There are some
vertical bands in the middle of the prefix (from about 3900-5900).  In
fact, one entire column (3900) is all busy numbers.  Use the cursor keys
to move the white cursor around the map. The number on the bottom right
corner will change and you'll see the result type and color for that
number.

     You can get a little or a lot from a .DAT map.  If the exchange is
a rural or residential one you'll probably see an even distribution of
result codes, with a certain level of each major result code.  Besides a
different number of timeouts, ringouts, or busys, most residential
exchanges look very similar - an even distribution with no pattern.

     In a business exchange you are much more likely to find patterns.
You may find a string or cluster of modems, a large range of similar
timeouts or voice responses, etc.  Ranges that are busy (like the 3900
column in our example) could be permanently busy, or some message which
the modem detects as a busy.  A series of ringouts could indicate part
of a PBX's DID (Direct Inward Dial) group.  It varies widely, and your
best bet is to always check it out manually - you never know what you'll
find.

     It behooves you to scan your prefixes and study your results.  It
is best to scan a prefix in one big scan (555-xxxx rather than 555-0xxx,
555-1xxx, etc) so you can see the whole prefix at once.  We would love
to have a look at your results and have a look at your .DAT files - try
to get in touch with us!  Who knows ... maybe your ToneMap will end
up on a T-shirt someday!

Hacking PBX's:
--------------

     If the PBX code is 4 digits or less you can use ToneLoc to hack it.
The simplest way is to use ToneLoc to look for an internal dialtone.
Lets say you found a 3 digit PBX at 555-9999 which hangs up on you after
you enter a bad code.  You'd use ToneLoc like this:

ToneLoc Example1 /m:555-9999Wxxx

(EXAMPLE1.DAT will be the .dat file, /m: specifies the mask.)

     This will produce dialing strings like this:  ATDT 555-9999Wxxx W;
ToneLoc will dial the number, wait for a dialtone, try a code, then wait
for a second dialtone.  If you get the right code, you'll get the second
dialtone, otherwise you'll just get a timeout.

    Some PBX's have alert tones for invalid codes which the W command
will hear as a dialtone.  You can't look for a second dialtone directly
with the W command on these PBX's, but Toneloc has a scan mode designed
specifically for this problem.  Set the scan mode to look for everything
except tones, either in the config file or on the command line, and use
ToneLoc like this:

ToneLoc example2 /m:555-8999WxxxW1

    This will produce dialing strings like this: ATDT 555-8999WxxxW1 W;.
Toneloc will dial the number, wait for the first dialtone, dial the
code, wait for a dialtone, dial 1, then wait for a dialtone.  If the
code is invalid, the second W command will hear the alert tones as a
dialtone and dial 1.  The tones should keep playing, and the third W
will respond to the alert tones too, giving a final response of Tone.
If the code is valid, the second W command will hear the internal
dialtone and the 1 will immediately quiet it since 1xx or 1xxx is a
valid extension on most PBX's.  This would give a final response of
Timeout since the third W command won't find a tone - and voila, you
have your code.  Are you confused yet?

     This method might not work if 1xx or 1xxx isn't a valid extension
on the PBX you are trying to hack, since some PBX's will immediately
give an alert tone if you dial the first digit of an invalid extension.
If you fail the first time around, and think you might have this
problem, have a look at the phone number for the PBX indial.  For
example, if the PBX indial is 555-4321, it's a good bet that some valid
DID extensions are in or near 4xxx, 3xx, or 2x.  Therefore, 4, 3 or 2 is
probably going to be the first digit of a valid extension, making them
good candidates for your terminal digit.

     Apparently some PBX's will respond with a carrier blast to an
invalid code, although we've never found one.  You can use the
everything-but-a-carrier scan mode for these, or just look for an
internal dialtone if carriers don't appear as tones to the W command.
(See Dual Scanning).

Cautions & Usage Notes:
-----------------------

     We do not have personal experience scanning 1-800 exchanges with
ToneLoc but we recommend that you exercise caution.  For a classic
example, see the Fall 1992 issue of 2600 magazine.  There is a letter
in there that Minor Threat received once after dialing about 100
1-800 numbers by HAND sequentially!  First of all, if you are are
looking for tones you may not get much.  Many of the PBX's or extenders
you would be looking for will answer with a short tone, about the length
of a ring.  That's how ToneLoc will perceive those tones - as a ring.
Many of the PBX's may also answer with silence, and need # or 9 to
activate their tone.  Local PBX's can answer like this as well, however
the 800 exchanges are more likely to have better security since they
are under constant pressure from call-sell operations as well as every
code abuser in the nation.  Second, MCI and Sprint can get irritated when
someone makes thousands of calls into their 800 exchange, and, unlike a
local number, they WILL have easy access to at least your area code and
exchange, and probably your entire phone number.  Since each 800 call
costs somebody money, and you aren't conducting legitimate business during
these calls, it might also be considered theft of service.

     Hacking an 800 system of any kind, be it a computer, long distance
extender, PBX, or even a VMB system, can be extremely risky.  We urge you
to use good judgment.  Find a local PBX and divert your call through it.

     If you live in an area with the Call Return, Call Trace, or
Caller ID active, you will definitely experience some call returns with
ToneLoc.  Politely explain to anyone who calls back that you dialed a
wrong number - don't provoke them into a Call Trace.  Who knows, you
may even meet a fellow hacker (It's happened to us - TWICE!).  If Caller
ID is active, use more caution - they could have your phone number and
scanning could be construed as harrassment, especially if it happens at
3:00 am.

     In any case, please use some intelligence if you are scanning a
range that belongs to a large company.  Often the same operator will have
to answer dozens of incoming phone numbers, and your strange hangups may
get tiresome enough in the course of the day that he or she might decide
to do something about it.  Listen in on ToneLoc to figure out what kind
of an exchange you are scanning.  If it is principally a business exchange,
consider only scanning at night when the affected businesses are closed.
If it is mostly residential you might want to scan during the day.  Make
intelligent use of the exclude mask to eliminate ranges that will most
likely be unproductive - unused ranges, pager numbers, answering services,
cellular phones, etc.  If you want an overview of your local exchanges,
first try the yellow pages.  You will quickly discover where promising
exchanges are.  If you want greater depth, go to your local public library
and ask at the reference desk for the criss-cross directory.  A section
of this directory is a listing of the telephone numbers in an exchange.
It does not list unlisted or nonpublished numbers (PBX's will not show up,
although the PBX billing number might), but it will show you if the
exchange is a residential one or not.  Ten minutes of thought can save
you 50 hours of scanning.

     When hacking a PBX, have some sense and do it late at night when
nobody is using the PBX.  Have a little patience; you'll be glad you did.
Make sure you hack RANDOMLY - sequential hacking is always a good
way to get noticed (although it probably won't make a difference in this
case), and besides ToneLoc has a better chance of finding the code sooner.

Is Scanning Illegal?  (Who cares)
--------------------

     We don't know.  We've heard it is legal to scan during business
hours when the call would not be harrasment.  We've heard it's not
illegal if you only call once.  We've heard that scanning with intent to
hack is illegal, as if such a thing could be proven.  (Some people
suggest not using the same phone line for hacking and scanning).
Remember, the most important thing is not whether it is illegal, but
whether you piss someone off or attract attention.

Here's what the staff at 2600 magazine have to say about wardialing:

     "In some places, scanning has been made illegal.  It would be hard,
though, for someone to file a complaint against you for scanning since
the whole purpose is to call every number once and only once.  It's not
likely to be thought of as harassment by anyone who gets a single phone
call from a scanning computer.  Some central offices have been known to
react strangely when people start scanning.  Sometimes you're unable to
get a dialtone for hours after you start scanning.  But there is no
uniform policy.  The best thing to do is to first find out if you've got
some crazy law saying you can't do it.  If, as is likely, there is no
such law, the only way to find out what happens is to give it a try."
[2600, Spring 1990, Page 27.]

Problems?  (Or; Why doesn't Toneloc work with my modem?)
---------

     ToneLoc's tone scanning mode may not work for everyone's modem.
ToneLoc looks for tones by dialing strings like this: "ATDT 555-1234 W;".
This tells the modem to dial the number 555-1234, wait for dialtone, and
then return to the command line.  ToneLoc then waits for a result code.
If it gets Ringing, Voice, Busy, etc. it moves on to the next number.
If it gets nothing, the modem never heard a dialtone, so ToneLoc hangs
up and moves on - this is a timeout.  If it gets "OK" as a result code
the modem has heard a tone (W waits for a dialtone) and returned to
the command line (semicolon (;) returns to the command line).

     ToneLoc won't work if your modem isn't discriminative.  Some cheap
modems "detect" dial tones just fine, but they also "detect" everything
else - rings, busys, even silence.  Other modems won't wait long enough,
and will move from W to ; very quickly.  If you have a problem that
doesn't stem from either of these, let us know and we'll see what we can
do to help.

Dual Scanning:
--------------

For a long time now we have been asked if it was possible to scan for
tones and carriers at the same time. At last, we have found a way.

The USR Courier 2400 was a great modem in it's time, and it is the best
modem we have found for scanning. The Courier 2400 has several unique
features. First, it detects carriers as tones. This means a tone scan
should pick up both carriers and tones, and a good number of falses.
This works fine, but it's a pain to manually sort out the carriers from
the many false responses you inevitably get when scanning for tones.

Recently, however, we discovered that the Courier 2400 will give a "NO
CARRIER" in response to a dialtone during a carrier scan. If you set your
ToneResponse to NO CARRIER in your config file, and scan for carriers,
you will pick up all the carriers and tones separately, on one pass!

There's a good chance other modems may behave similarly. Try it with
your modem and see. If you find one that works well, tell us about it
and we'll include a note in the next version of Toneloc. If not, you
should be able to pick up a used Courier 2400 for cheap.

Credits:
--------

     We hope you find this program useful. Give it to anyone and
everyone who deserves to have it.  If you think it is very cool and
useful, try to contact us somehow.  If you think it is a piece of shit
and the directions totally misguided, try to contact us anyway.  Our
handles are Minor Threat and Mucho Maas.  Minor Threat can be reached on
CelerityNet, our internet address <tone@ccwf.cc.utexas.edu>, or IRC.
Mucho Maas can be reached at the internet address as well.

     ToneLoc is written in C and assembly.  Assembled by Turbo
Assembler, and compiled by Borland C++ 3.1.  Window routines are from
CXL v5.2.  The built-in SERIAL routines are based on code from an
excellent book : "Serial Communications in C and C++" by Mark Goodwin.

Minor Threat Sez:

     Thanks to Alexis Machine and Marko Ramius for getting me started
phreaking.  Thanks to our beta testers, and thanks Alexander Bell for
inventing the telephone.  I know he had us in mind.

Mucho Maas Sez:

     Thanks to Minor Threat for helping me work on ToneLoc.  It should be
noted that the lion's share of the programming was done by him, and that
his code is a hell of a lot cleaner than mine.  Still, somebody had to
get Threat off his ass, and give him features to re-write.  Credit for
the PBX hacking technique described here goes to an old text file by Steve
Dahl.

------------------------------------------------------------------------------
One last quote: from a newspaper editorial in the 1870's

'... carrying human voice over copper wires is impossible, and even if
 it was possible, the thing would have no practical use.'

 HA!
