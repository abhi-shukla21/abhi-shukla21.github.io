## Requirements and Goals of the System

1. Users should be able to upload and download their files/photos from any device.
2. Users should be able to share files or folders with other users.
3. Our system should offer automatic synchronization across devices, i.e., action taken by user on one device should reflect on all the devices.
4. The system should support uploading large files upto a GB.
5. ACIDity (Atomicity, Consistency, Isolation and Durability) of transactions should  be followed.
6. Offline support.



**Extended Requirements:**

- The system should support snapshotting the data, so that the user can go back to any previous version.



## Some Design Considerations

- We should expect huge read write volumes.
- Read to write ratio should be nearly same.
- Internally, files should be stored in small chunks (upto 4MB). This provides a lot of benefits when it comes to retrying the failed uploads. We need to retry only the failed chunk and not the entire file.
- In case of updates, we should only sync the updated chunk.
- By removing duplicate chunks, we can reduce the storage space.
- Keeping a local copy of metadata (file name, size, etc) at the client will a lot round trips to the server.
- For small changes, user should intelligently upload the diff and not the entire chunk.



## Capacity Estimation and Constraints

- Let's assume we have 500M total users and 100M daily active user.
- Let's assume on average, one user connects to three devices.
- If each user has 200 files/photos, we will have 100 billion total files.
- If each file is of size 100kb, we will need 100PB total storage space.
- Let's also assume we have 1M active connections each minute.



## High Level Design

The user will specify a folder as workspace on his device. Any files/photos or folders kept in that folder will be synced with the remote server. Any delete/update operation performed on these files will be reflected the same way on the backend server also. Similarly the user can specify workspace on different devices and these work spaces will be synced together.

At a high level we need to store file and their metadata like file size, directory, who this file is shared with etc. So we need a server that can facilitate the user to upload the file **(block server)**. A server that can help maintaining the metadata table **(metadata table)** in SQL or NoSQL database. We also need a server that can notify all the users whenever there's some change in the workspace or a particular file so that they can sync it **(Synchronization server)**.

![](/assets/Dropbox_HLD.PNG)



## Component Design

