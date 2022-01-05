This is a text-based server using TCP.  It is multi-threaded, with 2 threads per connected user for read and writing.  It is very easy to deply on any server that has python3.

INCOMING MESSSAGES (sent by clients)

Join server - Provide a username and password.  If valid, the server will create a new session for you, and send you message saying connected with the session id.  It will mark that session as logged in.  All subsequent commands require logged in.  If you are in another session, it will end that session.

0:username:password\n (note, usernames and passwords cannot contain colons :s)

Get rooms - Assuming the session is logged in, you can get a list of public rooms.  The server will retrieve the list of public rooms from its database, including who is in those rooms and send them to the client.

1:password\n 

Join room - The server will put you in a valid room, or it will create a room and put you in it.  You will start receiving messages from that room, starting with a room_joined message, and then all subsequent messages.  

2:room:password\n 

2:-1\n - Leave room, this should choose a new room owner or something

Send message - You can say "all", "others", or list specific users that should receive the message.  

3:0:Message\n - others in room
3:1:Message\n - all in room
3:2:Message\n - others in the room, at once, meaning order is maintained
3:3:Message\n - all in room, order maintained

4:group:Message\n - message a specific group

5:group:c1:c2:c3\n - specify a named group of clients (used with 4)

OUTGOING MESSAGES (send by server)

Session_started - you know you are logged in, subsequent commands will succeed
0:[client_id]\n

rooms - a list of rooms on the server, with counts
1:r1-N,r2-N...\n

2:user_id:room_joined\n - room joined
2:user_id:\n - room left

3:user_id:Message\n - message from sender user_id

4:user_id\n - New master client


 



