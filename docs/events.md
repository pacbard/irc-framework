### Events available on an IRC client

Raw IRC events are parsed and turned into javascript friendly event objects. IRC events that are not parsed are triggered using their IRC command name.

You can bind to an event via the `.on` method.
~~~javascript
var client = new IRC.Client();
client.connect({
    host: 'irc.freenode.net',
    port: 6667,
    nick: 'prawnsbot'
});

client.on('registered', function(event) {
    // ...
});
~~~

Or if you want to use them in your middleware...
~~~javascript
function MyMiddleware() {
    return function(client, raw_events, parsed_events) {
        parsed_events.use(theMiddleware);
    }


    function theMiddleware(command, event, client, next) {
        if (command === 'registered') {
            // ...
        }

        next();
    }
}
~~~


#### Registration
**registered** / **connected**

Once the client has connected and successfully registered on the IRC network. This is a good place to start joining channels.
~~~javascript
{
    nick: nick
}
~~~


**reconnecting**

The client has disconnected from the network and will now automatically re-connect (if enabled).
~~~javascript
{ }
~~~


**close**

The client has disconnected from the network and failed to auto reconnect (if enabled).
~~~javascript
{ }
~~~


**socket close**

The client has disconnected from the network.
~~~javascript
{ }
~~~


**socket connected**

The client has a connected socket to the network. Network registration will automatically start at this point.
~~~javascript
{ }
~~~


**raw socket connected**

The client has a raw connected socket to the network but not yet completed any TLS handshakes yet. This is a good place to read any TCP port information for things like identd.
~~~javascript
{ }
~~~


**server options**
~~~javascript
{
    options: { ... },
    cap: { ... }
}
~~~


#### Channels
**channel info**
~~~javascript
{
    channel: '#channel',
    modes: [ ... ]
}
~~~


**channel info**
~~~javascript
{
    channel: '#channel',
    created_at: 000000000
}
~~~


**channel info**
~~~javascript
{
    channel: '#channel',
    url: 'http://channel-website.com/'
}
~~~


**userlist**
~~~javascript
{
    channel: '#channel',
    users: [ ... ]
}
~~~


**banlist**
~~~javascript
{
    channel: '#channel',
    bans: [ ... ]
}
~~~


**topic**
~~~javascript
{
    channel: '#channel',
    // topic will be empty if one is not set
    topic: 'The channel topic',

    // If the topic has just been changed, the following is also available
    nick: 'prawnsalad',
    time: 000000000
}
~~~


**topicsetby**
~~~javascript
{
    nick: 'prawnsalad',
    channel: '#channel',
    when: 000000000
}
~~~


**join**

The account name will only be available on supported networks.
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    gecos: 'prawns real name',
    channel: '#channel',
    time: 000000000,
    account: 'account_name'
}
~~~


**part**
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    channel: '#channel',
    message: 'My part message',
    time: 000000000
}
~~~


**kick**
~~~javascript
{
    kicked: 'someabuser',
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    channel: '#channel',
    message: 'Reason why someabuser was kicked',
    time: 000000000
}
~~~


**quit**
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    message: 'Reason why I'm leaving IRC,
    time: 000000000
}
~~~


**invited**
~~~javascript
{
    nick: 'inviteduser',
    channel: '#channel'
}
~~~



#### Messaging
**notice**

Also triggers a **message** event with .type = 'notice'. from_server indicates is this notice was
sent from the server or a user.
~~~javascript
{
    from_server: false,
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    target: '#channel',
    group: '@',
    message: 'A message to all channel ops',
    tags: [],
    time: 000000000
}
~~~


**action**

Also triggers a **message** event with .type = 'action'
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    target: '#channel',
    message: 'slaps someuser around a bit with a large trout',
    tags: [],
    time: 000000000
}
~~~


**privmsg**

Also triggers a **message** event with .type = 'privmsg'
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    target: '#channel',
    message: 'Hello everybody',
    tags: [],
    time: 000000000
}
~~~


**ctcp response**
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    target: 'someuser',
    message: 'VERSION kiwiirc',
    time: 000000000
}
~~~


**ctcp request**
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    target: 'someuser',
    type: 'VERSION',
    message: 'VERSION and remaining text',
    time: 000000000
}
~~~


**wallops**
~~~javascript
{
    from_server: false,
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'manchester.isp.net',
    message: 'This is a server-wide message'
}
~~~


#### Users
**nick**
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    hostname: 'isp.manchester.net',
    new_nick: 'prawns_new_nick',
    time: 000000000
}
~~~


**away**
~~~javascript
{
    nick: 'prawnsalad',
    message: 'Time to go eat some food.',
    time: 000000000
}
~~~



**nick in use**
~~~javascript
{
    nick: 'attempted_nick',
    reason: 'That nickname is already in use'
}
~~~


**nick invalid**
~~~javascript
{
    nick: 'attempted@nick',
    reason: 'That is an invalid nick'
}
~~~



**whois**

Not all of these options will be available. Some will be missing depending on the network.
~~~javascript
{
	away: 'away message',
	nick: 'prawnsalad',
	user: 'prawn',
	host: 'manchester.isp.net',
	actuallhost: 'sometimes set when using webirc',
    real_name: 'A real prawn',
	helpop: 'is available for help',
	bot: 'is a bot',
	server: 'irc.server.net',
	server_info: '',
	operator: 'is an operator',
	channels: 'is on these channels',
	modes: '',
	idle: 'idle for 34 secs',
	logon: 'logged on at X',
	registered_nick: 'prawnsalad',
	account: 'logged on account',
	secure: 'is using SSL/TLS',
	special: ''
}
~~~


**whowas**

If the requested user was not found, error will contain 'no_such_nick'.
~~~javascript
{
    nick: 'prawnsalad',
    ident: 'prawn',
    host: 'manchester.isp.net',
    real_name: 'prawns real name',
    error: ''
}
~~~
