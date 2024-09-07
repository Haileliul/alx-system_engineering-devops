# Postmortem: API Service Outage – August 5, 2023

## Issue Summary:
- **Duration**: 08:00 AM - 09:30 AM (UTC)
- **Impact**: 70% of users were unable to access API services, leading to failed form submissions, issues viewing projects, and inability to access the admin dashboard.
- **Root Cause**: A memory leak in the Node.js server caused unresponsiveness, resulting in failed requests.

---

## Timeline:
- **07:50 AM**: Monitoring detected elevated API response times.
- **08:00 AM**: Engineers received alerts of API timeouts affecting multiple endpoints.
- **08:05 AM**: Initial investigation focused on network issues and external dependencies (assumed Cloudinary was down).
- **08:20 AM**: External services confirmed operational; focus shifted to server-side issues.
- **08:30 AM**: Memory consumption on the Node.js server was identified as unusually high, with no memory reclamation.
- **08:40 AM**: Incident escalated to the infrastructure team to investigate resource bottlenecks and memory leaks.
- **09:00 AM**: Root cause identified: improper memory handling of large image uploads causing a memory leak.
- **09:10 AM**: Temporary fix applied by restarting the Node.js server.
- **09:30 AM**: Full service restored after a patch was deployed to manage memory properly during uploads.

---

## Root Cause and Resolution:
The issue was caused by a memory leak in the `/api/upload` endpoint due to improper handling of large image uploads. The system was loading entire files into memory, overwhelming the garbage collector.

**Resolution**:
- **Temporary fix**: Restarting the Node.js server to free up memory.
- **Permanent fix**: Refactored the image upload process to use streaming for file uploads and implemented file size limits on uploads.

---

## Corrective and Preventative Measures:
### Improvements:
1. **Memory Limits**: Enforce memory usage limits on the Node.js server.
2. **Improve Image Upload Handling**: Implement file streaming to prevent excessive memory usage.
3. **Enhanced Monitoring**: Add memory-specific monitoring and alerting.
4. **Audit API Endpoints**: Review and optimize memory handling across all API endpoints.
5. **Stress Testing**: Add stress testing to simulate high-load scenarios during CI/CD.

### TODO:
- [ ] Patch Node.js server for improved memory handling.
- [ ] Implement file streaming for image uploads.
- [ ] Add detailed monitoring for memory usage.
- [ ] Conduct an audit of all API endpoints.
- [ ] Add stress testing for file uploads to the CI/CD pipeline.
