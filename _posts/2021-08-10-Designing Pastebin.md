## What is Pastebin?

Pastebin like services allow users to paste text content or images over the network and generate unique URL to access uploaded data.



## Requirements and Goal of the System

**Functional Requirements**

1. Users should be able to upload or "paste" their data and get a unique URL to access it.
2. Only support text
3. Data and links will expire a specific timespan automatically. Users should also be able to specify expiration time.
4. Users should optionally be able to set custom alias for their paste



**Non-functional Requirements**

1. The system should be highly reliable. Any data uploaded should not be lost.
2. The system should be highly available. Otherwise the users will not be able to access  their pastes.
3. Users should be able to access their pastes in real time with minimum latency.
4. Pastes links should not be predictable.



**Extended Requirements:**

1. Analytics, e.g., how many times a paste was accessed.
2. Services should also be accessible through REST APIs by other services.



## Some Design Considerations

Pastebin shares some requirements with [URL shortening service](https://abhi-shukla21.github.io/2021/08/09/Designing-a-URL-shortening-service-like-tinyUrl.html), but there are some additional design considerations we should keep in mind.



**What should be the limit on the amount of text user can paste at a time?** We can limit this to 10MB to stop abuse of service.



**Should we impose size limits on custom URLs?** 

