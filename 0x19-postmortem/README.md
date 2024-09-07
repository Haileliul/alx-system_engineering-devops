Postmortem: API Service Outage – August 5, 2023
Issue Summary:
Duration: 08:00 AM - 09:30 AM (UTC)
Impact: 70% of users could not access the API services; requests were failing or timing out. Users experienced failed form submissions, inability to view projects, and inability to interact with the admin dashboard.
Root Cause: A memory leak in the Node.js server caused the server to become unresponsive, leading to failed requests.
Timeline:
07:50 AM: Monitoring systems began showing elevated response times on the API server, but within acceptable limits.
08:00 AM: Engineers received alerts about API timeouts and failures across multiple endpoints.
08:05 AM: Initial investigation started, focusing on network issues and external dependencies (assumed to be an outage on Cloudinary).
08:20 AM: The investigation shifted to server-side issues as external services were confirmed to be operational.
08:30 AM: Further debugging revealed memory consumption on the Node.js server was abnormally high, with no memory reclamation.
08:40 AM: Incident escalated to the infrastructure team to check for server resource bottlenecks and memory leaks.
09:00 AM: The root cause was identified as a memory leak caused by improper handling of large image uploads.
09:10 AM: Temporary fix applied by restarting the Node.js server to free up memory.
09:30 AM: Full service restored after applying a patch to properly handle memory management for large uploads.
Root Cause and Resolution:
The root cause was a memory leak in the Node.js server, triggered by improper handling of large image uploads to the /api/upload endpoint. The server was handling large image uploads without proper memory management, resulting in excessive memory allocation. The garbage collector was unable to free up memory fast enough, leading to eventual server unresponsiveness and request failures.

The issue was resolved by:

Restarting the Node.js server to clear the memory.
Adding better memory management in the image upload process by streaming image data rather than loading it all into memory.
Implementing file size limits on the /api/upload endpoint to prevent large file uploads from overwhelming the server.
Corrective and Preventative Measures:
To prevent similar incidents in the future, the following steps will be taken:

Implement Memory Limits:
Add memory usage limits to the Node.js server to automatically trigger alerts before memory consumption becomes critical.

Enhance Image Handling:
Refactor the image upload process to use streams, preventing entire files from being loaded into memory at once.

Increase Monitoring Coverage:
Set up detailed monitoring and alerts specifically for memory usage and file handling processes on the API server.

Review All API Endpoints:
Conduct an audit of all API endpoints to ensure that they handle memory efficiently and that file upload processes have safeguards.

Additional Testing:
Add stress testing to the CI/CD pipeline to simulate large file uploads and high memory consumption scenarios.

TODO:

 Patch Node.js server for better memory handling.
 Implement image streaming for uploads.
 Add monitoring for memory usage.
 Conduct a full audit of API endpoints.
 Add stress testing for file uploads in the CI/CD process.
This postmortem highlights the importance of proactive memory management and robust monitoring to ensure smooth operations and avoid outages.
