# System Design Briefing: Pagination Strategies
## 1. Executive Summary
Pagination is the process of dividing a large dataset into smaller, manageable chunks, or **"pages."**
It is a critical system design pattern for:
- Improving performance
- Reducing network load
- Enhancing user experience
Two primary methods for implementing pagination are:
1. **Offset-based Pagination**
2. **Cursor-based (Keyset) Pagination**
### A. Offset-Based Pagination
- Works like flipping through a book’s pages using **`LIMIT`** and **`OFFSET`**.
- **Advantages**:
    - Simple to implement
    - Allows jumping to any page number
- **Disadvantages**:
    - Performance degradation on large datasets
    - Data inconsistencies (missed/duplicated items) if data changes mid-navigation
- **Best For**: Small-to-medium, relatively static datasets (e.g., admin dashboards, reports).
---
### B. Cursor-Based Pagination
- Works like a **bookmark**, using a sequential identifier (ID or timestamp) as a cursor.
- **Advantages**:
    - Highly performant and scalable
    - Stable and consistent with dynamic datasets
- **Disadvantages**:
    - More complex to implement
    - No direct page number jumps
- **Best For**: Large, dynamic datasets (e.g., social media feeds with infinite scrolling).
---
## 2. Fundamentals of Pagination
Pagination avoids loading an entire dataset at once by **fetching discrete chunks**.
**Core Benefits:**
- **Reduced Network Load**: Less data transferred per request.
- **Improved Performance**: Faster page loads, smoother UI.
- **Decreased Database Strain**: Prevents massive queries scanning full tables.
**UI Variations:**
- Numbered page links
- Next/Previous buttons
- Load More button
- Infinite Scroll
---
## 3. Offset-Based Pagination
### A. Mechanism
- Uses `LIMIT` (items per page) and `OFFSET` (items to skip).
**Examples:**
- Page 1 → `LIMIT 10 OFFSET 0`
- Page 2 → `LIMIT 10 OFFSET 10`
- Page 3 → `LIMIT 10 OFFSET 20`
---
### B. End-to-End Implementation
1. **Front-End Request**
    - API call with `limit` and `offset`
    - Example:
        GET /api/posts?limit=5&offset=0
2. **Back-End Processing**
    - Translate into SQL query:
        ```sql
        SELECT * FROM posts ORDER BY created_at DESC LIMIT 5 OFFSET 0;
        ```
3. **API Response**
    - Returns JSON data + metadata
    - Example metadata:
        - currentPage
        - pageSize
        - totalItems
        - totalPages
        - hasNextPage
        - hasPreviousPage
4. **Front-End Rendering**
    - Render items + UI controls (e.g., disabling "Next" on last page).
---
### C. Analysis: Pros and Cons
|Pros|Cons|
|---|---|
|Simple to implement|Poor performance on large offsets|
|Allows direct navigation|Risk of missing/duplicated data if dataset changes|
**Optimal Use Cases:**
- Admin dashboards
- Reports
- Product catalogs
---
## 4. Cursor-Based (Keyset) Pagination
### A. Mechanism
- Uses a **cursor value** (timestamp or ID) from the last fetched item to get the next set.
- Example cursor: `created_at` or unique `id`.
---
### B. End-to-End Implementation
1. **Initial Request**
    ```sql
    SELECT id, title, created_at FROM posts ORDER BY created_at DESC LIMIT 5;
    ```
2. **Next Page Request**
    ```sql
    SELECT id, title, created_at
    FROM posts
    WHERE created_at < [cursor_timestamp]
    ORDER BY created_at DESC
    LIMIT 5;
    ```
3. **Previous Page Request**
    ```sql
    SELECT id, title, created_at
    FROM posts
    WHERE created_at > [cursor_timestamp]
    ORDER BY created_at ASC
    LIMIT 5;
    ```
    - Results reversed before sending to client.
4. **Back-End Processing**
    - Decode incoming cursor (if encoded).
    - Construct appropriate `WHERE` clause.
    - Generate new `next_cursor`.
    - Encode in Base64 (hides details & prevents tampering).
    **Example Encoded Cursor:**
    eyJjcmVhdGVkX2F0IjoiMjAyMy0xMC0yN1QwOToxNTowMC4wMDBaIn0=
---
### C. Analysis: Pros and Cons
|Pros|Cons|
|---|---|
|Highly performant & scalable|Cannot jump to specific page number|
|Stable results in dynamic datasets|More complex implementation|
**Optimal Use Cases:**
- Infinite scroll feeds
- Activity logs
- Real-time data streams
---
## 5. Infinite Scroll: A UI Variation
- Built directly on cursor-based pagination.
- **Mechanism**: Front-end detects scroll (e.g., using `IntersectionObserver`).
- **Trigger**: Fetch next page using stored cursor.
- **UX**: Seamless scrolling experience.
- **Examples**: Pinterest, Dribbble.
---
## 6. Real-World API Patterns
- **GitHub REST API**
    - Pagination links in **HTTP headers**.
    - Example:
        Link: [https://api.github.com/resource?page=2](https://api.github.com/resource?page=2); rel="next"
- **Atlassian Jira API**
    - Pagination metadata inside **JSON response body**.
**Key Takeaway:**
APIs must provide enough metadata so the client can understand pagination state, detect availability of more data, and know how to request it.
---