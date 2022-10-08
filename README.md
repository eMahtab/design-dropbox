# Design Cloud File Storage System e.g. Google Drive, Dropbox, Microsoft OneDrive

!["Design Dropbox"](cloud-file-storage.png?raw=true)

# Functional Requirements :

1. System should support Users to upload the files to the cloud storage.
2. System shouls support uploading all file types (.txt, .doc, .pdf, .png/jpeg, .zip, .exe, .ova) etc.
3. User should be able to sync files to cloud storage.
4. System should support max file size of 10 GB.
5. The owner(user who created the file/folder) of the file/folder should have all the access e.g. Read, Write and Share with others.
6. Owner of the file/folder should be able to share the file/folder with others (through a link or any other way)
7. The system should allow offline file creates/updates/deletes. Then as soon as the device comes online, it synchronizes the changes to the cloud. 
8. User should be able to see all the files/folders that user have created, and should be able to search for files through a User Interface.

# Non Functional Requirements :

1. System should be highly available
2. The system should be highly scalable as the number of users using the system increases


## Client :

Client(desktop or mobile app) keeps a watch on user’s workspace and synchronizes the files with remote server. Below are some main responsibilities of client:

1. Watch workspace for changes
2. Upload or download changes to file from remote server
3. Handle the conflicts due to offline or concurrent updates
4. Update file metadata on remote server if they change

# Save Bandwidth using File Chunking :
File chunking is very important part of the system. If there is any change in file, client determines which chunk has changed and just uploads that chunk to remote server. Similarly on other side, other client gets notified about the chunk that has changed and downloads just that chunk. This saves a lot of network bandwidth.

And this saving is significant when the file size is large, suppose initial file size was around 2 GB, after 5 minutes user updated the same file making it 2.01 GB, since the change was very small only the chunks which are changed or gets newly added will be uploaded. So the client app just needs to upload the chunks which were changed to the cloud. And if this file was shared with multiple other users, they just need to upload the chunks which were changed.

**Watcher:** Watcher monitors for file changes in workspace like update, create, delete of files and folders. Watcher notifies Indexer about the changes.

**Chunker:** Chunker splits the big files in to chunks of 4 MB. This also reconstructs the original file from chunks.

**Indexer:** Indexer listens for the events from watcher and updates the Client Metadata Database with information about the chunks of modified file. It also notifies Synchronizer after committing changes to Client Metadata Database.

**Client Metadata Database:** Client Metadata Database stores the information about different files in workspace, file chunks, chunk version and file location in the file system. This can be implemented using a lightweight database like SQLite.

**Synchronizer:** Synchronizer listens for events from Indexer and communicates with Meta Service and Block service for updating meta data and modified chunk of file on remote server respectively. It also listens for changes broadcasted by Notification Service and downloads the modified chunks from remote server.


# References :

1. https://www.youtube.com/watch?v=3RHjRXWAUvg

2. https://thinksoftware.medium.com/how-to-tackle-system-design-interview-for-file-sharing-service-like-dropbox-or-google-drive-7983fdbf1a82

3. https://systemdesignprimer.com/dropbox-system-design/
