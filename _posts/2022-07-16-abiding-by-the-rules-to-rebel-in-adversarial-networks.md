# Abiding by the Rules in Order to Rebel from Within Adversarial Networks

# Background
Okay, so today I'm going to take a stab at writing the blog article that I
should have written a few years ago. This is a blog about my time reverse
engineering the WeChat web application API/protocol in order to develop my own
unofficial API JavaScript library so that it could be used in our uProxy
project.

During this research, I didn't really know JavaScript very well, I had taken a
generic "web programming" class at UW just the quarter before, where we spent
about 2 weeks on how to use "JavaScript". So the first thing was to begin
learning how to code (better, effectively), in that language. I would spend some
of my time during the project just learning node.js so that I could create a
node.js library/package for my WeChat API functionality.

WeChat was of interest to us because of a few reasons...

First, The Chinese government is infamous for their Great FireWall (GFW).
(so named as a pun on the world-famous Great Wall of China that was used to
keep out the Mongolians way back). However, the GFW exists for another
purpose—in order to block internet content that the Chinese government
believes that its citizens should not be accessing. In other words, censorship;
though often the blocking is due to economical reasons. For example, popular
websites world-over such as Google, Facebook, Twitter, Github, etc. are blocked
in favor of their Chinese counterparts. Note, China has been known (at least in
certain circles) to steal Intellectual Property (IP) in order to bootstrap some
of their own spinoffs of popular platforms/technologies. I don't have much to
say on the matter other than it sounds rude and nationalistic to me; but on
the other hand, it seems like a potentially good thing if the Chinese
counterparts are able to innovate in ways that maybe the other version of
X hadn't thought of or vice versa—the quintessential capitalist
argument for competition: to drive innovation. Though, I doubt that that's
their intention.

Second, WeChat is _immensely_ popular. I heard that in China, people don't
really ask for each other's phone numbers, they just ask for their WeChat
identities.

Therefore, adding it as an option for people to connect/proxy through each
other would be huge for the uProxy project, as it would allow us to provide
access to so many people.

However, there was a major distinctive factor to consider here. The other
"Social Provider"'s we had for uProxy were... for lack of a better word,
consenting. They voluntarily published API's that would allow us to use their
platform as a communications channel for brokering a direct connection between
the censored user and the connection sharer. WeChat did not publish an API for
interacting with WeChat servers in order to send messages and such. However, we
know that it did have the APIs, they just might be undocumented, since they had
a web client for the platform. But since this would be an unauthorized usage of
their API, we would be playing with fire a bit. Not to mention that WeChat is
owned by Tencent, one of the largest conglomerates in China. While I don't have
proof, I do make the assumption that the Chinese government works fairly
closely with Tencent.


# Where to even begin
First thing's first, I needed to learn how this system worked. I had never used
WeChat as an app, so I was new to the whole thing entirely. Not to mention the
fact that pretty much any information about WeChat was in Chinese.

WeChat is primarily a mobile app, but it does have a web client version of it.
It requires you to scan a QR code using the mobile app in order to login and
use the web app version of the client. I started by downloading the app on an
old Android phone I had lying around. Once I had the app, I needed friends to
add in that I could really play with, as the whole point of the app was the
social parts. Thankfully, the graduate student I was working under had the app
on _his_ burner, so I was able to add him. The Networking lab also provided me
with another old Android phone that I could use, so I made another WeChat
account there so I could bounce messages between them and see how things came
out. Within the app there is a type of "feed" similar to facebook, as wll as
the classical messaging functions. Notably, the web app was a neutered version
of the mobile app, and so upon logging into the web app, you find that the only
functionality in there is the contacts list and the messaging capabilities.
Cool, that's fine, I didn't need that feed thing anyways!

# Okay, where's the X?
Focusing on the web app, I poked around to see what I could find. There were
all of the contacts that I had added, but there were a few contacts in there
that were in by default. One was like the WeChat support, and another was
similar to "slackbot" on Slack. Essentially, a fake user you could leave notes
to yourself with, etc. When typing, there were indications of typing, to the
other user. You could create group chats as well, which would make separate
"chat rooms" for the members of the group chat. You could send video, audio,
and location data messages to your recipient. So, yeah, pretty standard stuff,
maybe with a little bit more flair... think Snapchat mixed with WhatsApp as far
as the web app went.

What's the beautiful thing about Web Apps? Oh, that's right, all of the
JavaScript code is public/client-side. Which means, we can look at everything
that the app is actually doing.

Opening up the developer tools, and clicking around on the Javascript being 
loaded I come across this gem: `webwxApp26a1b6.js`. 

TODO: (seems promising OR go on OR I'm listening OR jackpot OR bingo) image
Jackpot. 
_(Note: WeChat is called WeiXin in Chinese, it's often seen abbreviated as wx)_

Here's a snippet of the code I see (after prettyifying it)

```javascript
!function() {
    var e, t = function() {
    }, o = ["assert", "clear", "count", "debug", "dir", "dirxml", "error", "exception", "group", "groupCollapsed", "groupEnd", "info", "log", "markTimeline", "profile", "profileEnd", "table", "time", "timeEnd", "timeStamp", "trace", "warn"], n = o.length;
    0 == /mmdebug/.test(location.search) && location.href.indexOf("dev.web.weixin") < 0 && (window.console = {});
    for (var r = window.console = window.console || {}; n--; )
        e = o[n], r[e] || (r[e] = t)
}(), angular.module("Controllers", []), !function() {
    "use strict";
    location.href.indexOf("dev.web") < 0 ? angular.module("exceptionOverride", []).factory("$exceptionHandler", [function() {
            return function(e) {
/*
Etc. ... 
*/
```

Allllllright, so... what am I looking at. Well... I see the word "angular" a
lot. Which, yep, this looks like an Angular.js app. Neat. Okay, what else do we
see? Those variables names are single character ones and useless basically.
Okay, great, so this has had minification done to it in order to reduce the
size of it (and somewhat to obfuscate it to my sensitive mortal eyes.

Great so... how do we go about looking over and figuring out what this all does
without my eyes bleeding anymore? There's like 3602 lines of JavaScript to go
over here. Two techniques come to mind: Ctrl+F, and the networking tab of the
developer tools console. Or at least, a combination of both. Honestly, we just
need to see what requests are being made, and then figure out how the data sent
in those requests is generated within the app. The rest of the code is fairly
irrelevant to us. That doesn't mean I didn't start by trying to rename all of
the variables in here—I hadn't even heard of minification or knew how Angular
worked at this point, so I figured that was the best course of action.

# Fortune favors the bold
I did the "look for a network request, Ctrl+F for values in the Javascript"
technique for quite a while... to great success, actually! But I kept watching
the web app looking at the javascript when I got super lucky.

## They uploaded a non-minified version of their web app code.

`:awwwww_yeah:`

Looking through this, I found lolz such as
![explitives in comments Screenshot]()

but I also found super helpful things such as _labeled enums_ of the different
messages that could be sent.

![MSGTYPE Enum Screenshot]()

Looking at these, there were some interesting names. Notably, I had seen some
of them when interacting with the app. But the most notable was the type 51
message, or STATUSNOTIFY. I saw some of those messages going across but they
didn't contain any text, and I wasn't sure what was in them.

After a time, I reasoned that the message had to do with the typing indication,
as it would go across when I started type a message. Neat. Okay, so... the
phone/web app doesn't show a message going across, yet the recipient does
receive a message... this property appeals to me. 

NOTE: MEME, me gusta? Something.

Why is this such a desirable trait? Well, in order to establish a WebRTC
(direct) connection for uProxy, we need to exchange some data between the
clients so that they agree on how to directly connect for the proxy session. We
wouldn't want to clog up your message history with your friend with a bunch of
JSON blobs, now would we? That wouldn't be a great user experience... So, I
wonder if we can send our message and just give this STATUSNOTIFY type?

YEP, SHIT YES, MOTHERFUCKER, WE CAN. WE'VE JUST ESTABLISHED A SUPER DUPER
SECRET COMMUNICATION CHANNEL OVER WECHAT, THAT ONLY PEOPLE USING _MY_ CLIENT(S)
CAN UNDERSTAND

![Ace Ventura humping air in accomplishment]()

Sorry, I got excited. It was a pivotal moment, getting this working. LET'S HOPE
I DON'T SCREW IT ALL UP

# Screwing it all up
Queue the "It's always Sunny" music, here we go. So, it was around this time
that the uProxy team discovered that people didn't like all of the contacts
from `<insert social provider here>` knowing that they use uProxy, or didn't
want to be able to see contacts in their "friends list". Kinda makes sense,
there's tons of people I have added on Facebook that I wouldn't feel
comfortable reaching out asking to share their connection with me or vice
versa. Admittedly, I'm also the one living in the US, so it's also possible for
people in the censored places to be friends with pro-censorship peoples that
they wouldn't want to disclose that they're using unauthorized proxy software
too. This feature request totally makes sense!

In WeChat, I can't really _not_ have all of your contacts. The way their API is
set up is so that you ask for the contact list, you get the contact list. No
ifs ands or buts about it, you get the whole thing or nothing at all. Well,
great, okay, so I'll just hide the contacts that aren't your friend... okay,
wait so who's your "friend" on uProxy, but _not_ your friend on WeChat then?
Crap. We needed a way to denote uProxy friends from the rest of the swill in
your WeChat contacts. 

But wait, there's more! We not only needed to know who was your uProxy friend
vs your WeChat contact, but we also needed/wanted to know who was _online_ of
your contacts. Well, WeChat doesn't really have presence, so we needed a way to
figure it out. So what should I do? Well, first idea was that I would blast out 
a message to your friends when you logged in to your uProxy WeChat account
so that they would know when you came online. Seems like a good idea, right?
Sure, yeah, let's do it... but first, let's make the messages _not_ hidden so 
that I can see them, and know if they're properly coming across.

Well shit. Guess what, it did exactly that, it send out the JSON "I'M ONLINE
NAO" uProxy blob to all of your contacts. Remember how there's a automatically
added contact for WeChat support added into your contact list? Cool, well I
didn't! So this blob gets sent to them, and I get the automated reply along the
lines of "Great, so glad you're enjoying WeChat, we'll look into what you've
said to us".

![Aunt Agatha, my existential dread is acting up]()

Well... this isn't too bad right? This will all just blow over, and it'll be
fine, I doubt they're really going to look into this whole thing very much.

# Not blowing over, getting looked into
Shortly after this, my hidden message trick stopped working. Every message send
with the STATUSNOTIFY type that had text sent with it was now displayed to
users, both in the phone and on the web app. yay... okay, great, so is there
another way I can make "hidden" messages happen, so that our user experience
isn't completely _tarnished_?







