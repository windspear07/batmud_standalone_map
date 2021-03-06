
copy from http://www.bat.org/forums?a=view_post&postid=477&page=&group_name=archive16

---

We've decided to open up the server->client control codes (the special client-server communication) for our thriving game client script/plugin developer community.

See below for some of the goodies:

The base syntax of an opening control code is:
ESC < nn

The control code is closed:
ESC > nn

The opening tag is terminated with the following string (only required for tags that accept arguments:
ESC |

Hence, a usable control code string could look like (this would print "This is a test" in red):
ESC<20FF0000ESC|This is a testESC>20

The control codes stack, i.e. the following is a valid output:
ESC<20FFFFFFESC|ESC<210000FFESC|Test output, white on blueESC>21ESC>20

The following set of control codes will be sent by the game server to the client as part of the normal text output:

CODE
nr     opening tag     closing tag     Description     Example
00     ESC < 00     ESC > 00     Closes any open control code tags and resets text properties and output into default window     ESC<00ESC>00
05     ESC < 05     ESC > 05     Signifies that the connection was successful     ESC<05ESC>05
06     ESC < 06     ESC > 06     Signifies that the connection failed with the reason given as arg     ESC<06Incorrect password.ESC>06
10     ESC < 10     ESC > 10     Defines the output to be a message of type <arg>     ESC<10chan_salesESC|Test outputESC>10
11     ESC < 11     ESC > 11     Clears the active screen     ESC<11ESC>11
20     ESC < 20     ESC > 20     Sets the text foreground color to be the RGB value specified as argument     ESC<2000FFFFESC|TestESC>20
21     ESC < 21     ESC > 21     Sets the text background color to be the RGB value specified as argument     ESC<21FF0000ESC|TestESC>21
22     ESC < 22     ESC > 22     Sets the text output to bold mode     ESC<22TestESC>22
23     ESC < 23     ESC > 23     Sets the text output in italic     ESC<23TestESC>23
24     ESC < 24     ESC > 24     Sets the text output as underlined     ESC<24TestESC>24
25     ESC < 25     ESC > 25     Sets the text output to blink     ESC<25Text is blinkingESC>25
29     ESC < 29     ESC > 29     Resets the text properties (reverts back to default colors)     ESC<29ESC>29
30     ESC < 30     ESC > 30     Sets the text to be a hyperlink to the link provides as argument     ESC<30http://www.bat.orgESC|BatMUD's homepageESC>30
31     ESC < 31     ESC > 31     Sets the text to be an in-game link as provided by argument     ESC<31northESC|Go northESC>31
40     ESC < 40     ESC > 40     Clears spell/skill progress indicator     ESC<40ESC>40
41     ESC < 41     ESC > 41     Relays the spell action status. Format:%s %d (spell rounds_left(1..n),0=unknown)     ESC<41magic_missile 2ESC>41
42     ESC < 42     ESC > 42     Relays the skill action status. Format:%s %d (skill rounds_left(1..n),0=unknown)     ESC<42bladed_fury 5ESC>42
50     ESC < 50     ESC > 50     Relays the full player health status. Format:%d %d %d %d %d %d (hp hpmax sp spmax ep epmax)     ESC<50100 200 200 250 300 350ESC>50
51     ESC < 51     ESC > 51     Relays the partial player health status. Format:%d %d %d (hp sp ep)     ESC<51100 200 200ESC>51
52     ESC < 52     ESC > 52     Relays the player name, surname, race, level, gender(0/1/2 neuter/male/female) and experience. Format:%s %s %s %d %d %d     ESC<52Ulath Pulath coder 100 1 1345323ESC>52
53     ESC < 53     ESC > 53     Relays the player free experience. Format:%d     ESC<531345323ESC>53
54     ESC < 54     ESC > 54     Relays the player status. Format:%d %d %d(unconscious stunned dead)     ESC<540 0 0ESC>54
60     ESC < 60     ESC > 60     Relays the player location. Format:%s %s %d %s %d %d %d(name race gender continent X Y Z)     ESC<60ulath coder 1 laenor 5100 5200 0ESC>60
61     ESC < 61     ESC > 61     Relays party location information. '0' means empty. Format:%s %d %d(player X Y)     ESC<61ulath 1 1ESC>61
62     ESC < 62     ESC > 62     Relays a full party status update. Format: %s:<player> %s:<race> %d:<gender> %d:<level> %d:<hp> %d:<maxhp> %d:<sp> %d:<maxsp> %d:<ep> %d:<maxep> %s:<status_flag> %d:<party_exp> %d:<total_party_exp> %d:<party_length_in_seconds> %s:<party_creation_time>     See examples below
63     ESC < 63     ESC > 63     Notifies that a player has left the party. Format: %s:<player>     ESC<63ulathESC>63
64     ESC < 64     ESC > 64     Relays status affecting spell information. Each effect has its own message. effect string is '_' padded. A time left=0 means that the effect has stopped. A item left=-1 means that the spell works on some other variable than time. Format: %s:<effect> %d:<time left in seconds>     ESC<64lay_on_hands 120ESC>64
70     ESC < 70     ESC > 70     Relays the set target information, 5% accuracy. '0 0' means that there is no target, and any formed targets should be cleared. Format: %s:<target> %d:<percentage>     ESC<70evilmonster 45ESC>70
99     ESC < 99     ESC > 99     Custom information relay. Format:%d %s %d (format (0=string, 1=int), type, data)     ESC<991 dex 300ESC>99


Some examples:
ESC<10mapESC|ESC<11ESC>11ESC>10 - clears the map window
ESC<10chan_swe+ESC|ESC<20FFFF00ESC|Gore <swe+>: detta är något på svenskaESC>20ESC>10

*) If party_position_X is 6, it means player is out of formation

Health updates:
A player with with 102/1001hp, 500/600sp, 300/400ep
ESC<51102 1001 500 600 300 400ESC>51

A player who is stunned, but not dead or unconscious
ESC<540 1 0ESC>54

A player by the name of 'Killer', race 'orc', level 50, gender male, with 123456 experience points
ESC<52Killer orc 50 1 123456ESC>52

Party updates:
A full party status update for player 'Killer', race 'orc', gender male, level 50, 101/200 hp, 202/303 sp, 404/504 ep, party leader for party 'ekuva ja expaa' and creator, at party position 1,1 (see 'Client status flag'), with 12345 exp gained during the party, 100000 total party exp and party length of 1234 seconds. Party create at utime 1234567
ESC<62Killer orc 1 50 101 200 202 303 404 504 ekuva_ja_expaa 1 1 1 0 0 0 0 1 0 0 0 0 0 0 0 12345 100000 1234 Wed_Oct_31_15:57:52_2007ESC>62

Client status flag:
The status flag is a string of integers, separated by spaces.

Each number represents a certain piece of information. For example, if bit number 1 (b = STUNNED) is 1, it means the player is currently stunned. If it's 0, the player is not stunned.

[p o n m l k j i h g f e d c b a]

a = UNCONSCIOUS
b = STUNNED
c = DEAD
d = INVISIBLE
e = IDLE
f = RESTING
g = LINKDEAD
h = LEADER
i = FOLLOWING
j = ENTRY
k = MEMBER
l = FORMATION
m = CREATOR
n = PLACE_Y
o = PLACE_X
p = PARTY_NAME (with whitespace ' ' replaced with '_')

Further Examples:

So, let's assume a player at party position 1,1 who is leading party 'expaa' and party creator.

The status flag would then be: "expaa 1 1 1 0 0 0 0 1 0 0 0 0 0 0 0"

Let's assume a player at party position 2,2, who is following in party 'ekuva meille' , idle and invisible

The status flag would then be: "ekuva_meille 2 2 0 0 0 0 1 0 0 0 1 1 0 0 0"

Communication channels:
All communication channels are in the format chan_<channelname>, i.e. chan_sales, chan_swe+, chan_mage

In addition, some special channels are used for game messages:

    spec_map - the map and long desc for new worldmap rooms
    spec_battle - battle messages
    spec_news - news reader
    spec_skill - skill output
    spec_spell - spell output