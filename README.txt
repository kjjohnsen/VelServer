This is a text-based server using TCP/UDP.  Note that means all messages sent and received by it are human-readable (though many will likely be base64 encoded binary).  It is multi-threaded, with 2 threads per connected user for read and writing TCP + a single UDP thread.  It is very easy to deply on any server that has python3, and has very little code (1 file), so it should be easy to understand/modify to fit particular needs. 

INCOMING MESSSAGES (sent by clients over TCP or UDP)

Join server - Provide a username and password.  Currently, the password is ignored, but likely you would want to reject users that did not use a correct password of some sort.  If valid, the server will create a new session for you, and send you message saying connected with your user id.  It will mark that session as logged in.  All subsequent commands require logged in.  

TCP - 0:username:password\n (note, usernames and passwords cannot contain colons, unless you do some kind of encoding scheme)
UDP - 0:userid - Required to establish a UDP connection.  Sending this before having a userid will fail.  Also, this is required to be from the same IP as the TCP connection or it will fail.  

Get rooms - The server will retrieve the list of public rooms from its database with the count of users in each room.  This could potentially use a password to get private rooms, though no implementation is provided for this purpose.

TCP - 1:password\n 

Join/Leave room - The server will put you in a valid room, or it will create a room and put you in it.  You will start receiving messages from that room, starting with a room_joined message, and then all subsequent messages.  Joining/leaving cause messages to be sent to people in your new/current room.  

TCP - 2:room:password\n - Join room

TCP - 2:-1\n - Leave room 

Send message - You can say "all", "others", or list specific users that should receive the message.  

TCP - 3:0:Message\n - others in room
TCP - 3:1:Message\n - all in room
TCP - 3:2:Message\n - others in the room, at once, meaning order is maintained with other ordered messages
TCP - 3:3:Message\n - all in room, order maintained

TCP - 4:group:Message\n - message a specific group

TCP - 5:group:c1:c2:c3\n - specify a named group of clients (used with 4).  This is client specific.  There is no way to specify a room-wide group.  Clients are responsible for updating it.  

OUTGOING MESSAGES (send by server)

Session_started - you know you are logged in, subsequent commands will succeed over that channel

TCP - 0:[client_id]\n
UDP - 0:[client_id]:connected (note, you shouldn't try to send UDP messages unless this succeeds)

Rooms - a list of rooms on the server, with counts of users
TCP - 1:r1-N,r2-N...\n

Users Joined or Left.  These are sent all people in the room.  New users will get a message for existing users automatically.

TCP - 2:user_id:room_joined\n - room joined
TCP - 2:user_id:\n - room left

Messages - Despite how they are sent (normal, ordered, groups), messages are always received from someone else on the server.  Messages always arrive in the same way they are sent (TCP/UDP).

TCP - 3:user_id:Message\n 
UDP - 3:user_id:Message 

Master Client - Happens at the start, when new clients join, and when the current master client leaves
TCP - 4:user_id\n


 



