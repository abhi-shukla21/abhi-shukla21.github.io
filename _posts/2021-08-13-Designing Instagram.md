## What is Instagram 

IG is a social n/w service that enables its users to upload and share their photos and videos with other users privately and publicly.

We plan to design a simple version of IG for this design problem, where a user can share photos and follow other  users. The 'News Feed' for the user will consist of top photos of all the people the user follows.



## Requirements and Goals of the System 

**Functional Requirements:**

1. Users should be able to upload/download/view photos
2. Users can perform searches based on photo/video titles.
3. Users can follow other users.
4. The system should generate and display a user's News Feed consisting of top photos from all the people user follows

**Non-functional Requirements:**

1. Our service needs to be highly available.
2. The acceptable latency of the system is 200ms for news feed generation
3. Consistency can tale a hit (in the interest of availability) if a user doesn't see a photo for a while, it should be fine.
4. The system should be highly reliable; any uploaded photo or video should never be lost.

**Not in scope**: Adding tags to photos, searching photos on tags, commenting on photos, tagging users to photos, who to follow, etc.



## Some Design Considerations

1. The system would be read-heavy so we will focus on building a system that  retrieve photos quickly.
2. Users can upload as many photos as they like, therefore efficient management of storage should be a crucial factor in designing this system.
3. Low latency is expected while viewing photos.
4. Data should be 100% reliable. If a user uploads a photo, the system will guarantee that it will never be lost.



## Capacity Estimation and Constraints

- Let's assume we have 500M total users, with 1M daily active users.
- 2M new photos every day, 23 new photos every sec.
- Avg photo size => 200KB
- Total space required for one day photos = 2M * 200KB => 400GB
- Total space required for 10 years = 400GB * 365 * 10 ~= 1425TB



## High Level System Design

At a high level, we need to support two scenarios, one to upload photos and the other to view/search photos. Our service would need some object storage servers to store the photos and some database servers to store metadata information about the photos.

![Instagram-HLD](/assets/Instagram-HLD.PNG)