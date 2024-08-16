### **Features Required:**

**1\. Document Creation:**

* **Functionality:** Users can create new documents with a title and optional metadata such as author name, creation date, and document tags.  
* **Data Schema:** Document schema includes fields for `title`, `content`, `metadata` (author, creation date, etc.), and `revision history`.

**2\. Real-Time Collaboration:**

* **Functionality:** Multiple users can edit the document simultaneously. Changes made by any user are immediately reflected across all connected clients.  
* **Implementation:** Use WebSocket connections for real-time updates and synchronization.

**3\. Version Control:**

* **Functionality:** Maintain a history of document revisions, allowing users to view, revert to, or compare previous versions.  
* **Data Schema:** Revision history includes version ID, timestamp, changes made, and author information.

**4\. Concurrency Management:**

* **Functionality:** Manage simultaneous edits by different users. Ensure document consistency and resolve conflicts as they occur.  
* **Implementation:** Use algorithms like Operational Transformation (OT) or Conflict-Free Replicated Data Types (CRDTs) to handle concurrent changes.

**5\. User Authentication:**

* **Functionality:** Users must log in to access and edit documents. Personalized access ensures secure and controlled document editing.  
* **Implementation:** Implement authentication mechanisms (e.g., OAuth, JWT) to manage user sessions and permissions.

**6\. Document Sharing:**

* **Functionality:** Share documents with specific users or groups. Control access permissions (e.g., view-only, edit) based on user roles.  
* **Implementation:** Design access control lists (ACLs) and sharing settings within the document metadata.

**7\. Undo/Redo Functionality:**

* **Functionality:** Allow users to undo or redo recent changes within their editing session to correct mistakes or experiment with edits.  
* **Implementation:** Maintain a change log or stack for tracking and reverting changes.

**8\. Conflict Resolution:**

* **Functionality:** Automatically handle conflicts that arise from simultaneous edits. Provide tools or algorithms to resolve conflicts without user intervention.  
* **Implementation:** Use conflict resolution algorithms like OT or CRDTs. Optionally provide manual conflict resolution tools if needed

**9**.  **Autosave:**

* **Functionality:** Automatically save the document at regular intervals or after significant changes to prevent data loss.  
* **Implementation:** Implement periodic save operations and a mechanism to handle autosave triggers.

### **Data storage:**

The key challenges include:
1. **Data Schema**: Designing a structure to store the document's current content, metadata, and other relevant information.  
2. **Revision Management**: Creating a system to track changes made by different users over time, allowing for features such as version history, undo/redo, and conflict resolution.  
3. **Concurrency and Consistency**: Ensuring that the document remains consistent across all users, even when multiple edits are made concurrently.

![Screenshot 2024-08-16 001613](https://github.com/user-attachments/assets/0fc30e0f-1d81-4e1c-adff-5130bdc287bb)

`Id` \- Unique identifier for the document.   
`Title` \- Name of the document.      
`Content` \- Current content of the document.  
`Metadata` \- Contains creation date, last modified date, author, etc.   
`Revisions` \- References to all revisions associated with this document.

![Screenshot 2024-08-16 002146](https://github.com/user-attachments/assets/5cc570ec-ab8d-4291-b84d-ca90f5c6d3d8)

`ID` \- Unique identifier for the revision.  
`Document ID` \- Identifier of the document to which this revision belongs.  
`Timestamp` \- Time when this revision was created.    
`UserID` \- Identifier of the user who made the changes.  
`Changes` \- Specific changes made in this revision (e.g., diff/patch)  
`PreviousRevisionID` \- Identifier of the previous revision.

![Screenshot 2024-08-16 002007](https://github.com/user-attachments/assets/8204fa68-1151-4ea4-baf3-af7021e85bc8)

`ID` \- Unique identifier for the user.  
`Name` \- User's name.  
`Email` \- User's email address.  
`Documents` \- List of documents owned or accessible by the user.

### **API Endpoints:**

### **1\. User Authentication and Management**

**Endpoint: `POST /api/register`**

* **Purpose**: To register a new user in the system.  
* **Input**: User details such as name, email, and password.  
* **Output**: A success message with the user ID or an error if the registration fails.

**Endpoint: `POST /api/login`**

* **Purpose**: To authenticate an existing user.  
* **Input**: Email and password.  
* **Output**: A success message with a token (JWT or session ID) for authenticated sessions.

**Endpoint: `GET /api/user/:id`**

* **Purpose**: To retrieve user information.  
* **Input**: User ID (as a URL parameter).  
* **Output**: User details such as name, email, and associated documents.

### **2\. Document Management**

**Endpoint: `POST /api/documents`**

* **Purpose**: To create a new document.  
* **Input**: Document title, content, and optional metadata.  
* **Output**: The created document's ID and details.

**Endpoint: `GET /api/documents`**

* **Purpose**: To retrieve all documents associated with the authenticated user.  
* **Input**: (Optional) Filters like date range or document title.  
* **Output**: A list of documents with basic information (ID, title, last modified).

**Endpoint: `GET /api/documents/:id`**

* **Purpose**: To retrieve a specific document by its ID.  
* **Input**: Document ID (as a URL parameter).  
* **Output**: Full document details, including title, content, metadata, and revision history.

**Endpoint: `PUT /api/documents/:id`**

* **Purpose**: To update an existing document.  
* **Input**: Document ID (as a URL parameter) and new content or metadata.  
* **Output**: The updated document's details.

**Endpoint: `DELETE /api/documents/:id`**

* **Purpose**: To delete a document by its ID.  
* **Input**: Document ID (as a URL parameter).  
* **Output**: A success message or an error if the document couldn't be deleted.

### **3\. Revision Management**

**Endpoint: `POST /api/documents/:id/revisions`**

* **Purpose**: To save a new revision of a document.  
* **Input**: Document ID (as a URL parameter) and changes made by the user.  
* **Output**: The created revision's ID and details.

**Endpoint: `GET /api/documents/:id/revisions`**

* **Purpose**: To retrieve all revisions of a specific document.  
* **Input**: Document ID (as a URL parameter).  
* **Output**: A list of revisions, each with its timestamp, user ID, and changes.

**Endpoint: `GET /api/documents/:id/revisions/:revisionId`**

* **Purpose**: To retrieve a specific revision by its ID.  
* **Input**: Document ID and Revision ID (as URL parameters).  
* **Output**: Full details of the specific revision.

**Endpoint: `POST /api/documents/:id/revert`**

* **Purpose**: To revert a document to a specific revision.  
* **Input**: Document ID and Revision ID (as URL parameters).  
* **Output**: The document reverted to the specified revision.

### **4\. Real-Time Collaboration**

**Endpoint: `POST /api/documents/:id/collaborators`**

* **Purpose**: To add collaborators to a document.  
* **Input**: Document ID (as a URL parameter) and collaborator details (user IDs).  
* **Output**: A success message with the list of current collaborators.

**Endpoint: `GET /api/documents/:id/collaborators`**

* **Purpose**: To get a list of all collaborators for a specific document.  
* **Input**: Document ID (as a URL parameter).  
* **Output**: A list of user IDs and their roles (e.g., editor, viewer).

**Endpoint: `POST /api/documents/:id/subscribe`**

* **Purpose**: To subscribe to real-time updates for a document.  
* **Input**: Document ID (as a URL parameter).  
* **Output**: A success message indicating that the client will receive live updates.

### **Data structures used for tracking document changes and user sessions:**

**Primary Data Structure: `Revision`**

* **Purpose**: To store the details of each change made to a document.  

* **Attributes**:  
  * `id`: A unique identifier for each revision.  
  * `timestamp`: The time at which the change was made.  
  * `userId`: The ID of the user who made the change.  
  * `changes`: A detailed record of the specific changes made.  
  * `previousRevisionId`: A link to the previous revision, enabling us to trace back through the document's history
 
  * **Pseudo Code**:
  ```
  struct Revision {  
    int id;  
    string timestamp;  
    int userId;  
    string changes;  
    int previousRevisionId;
  }
  ```

  **Explanation**: The `Revision` structure holds all the necessary information about a single change to the document. By linking each revision to the previous one (`previousRevisionId`), we create a chain of revisions, allowing us to navigate backward and forward through the document’s history. This is crucial for supporting undo/redo operations.

**Secondary Data Structure: `Document`**

* **Purpose**: To represent the document itself and manage its revisions.  
* **Attributes**:  
  * `id`: Unique identifier for the document.  
  * `title`: The title of the document.  
  * `content`: The current content of the document.  
  * `revisions`: A vector of `Revision` objects representing the document's revision history.
 
```
struct Document {  
    int id;  
    string title;  
    string content;  
    vector<Revision> revisions;  
};
```

**Explanation**: The `Document` structure contains all revisions related to the document. Each time a user makes a change, a new `Revision` object is created and added to the `revisions` vector. This approach allows us to maintain a complete history of all changes, making it easy to access any previous state of the document.

#### **2\. Tracking User Sessions**

User sessions are critical for managing which users are currently editing the document, what changes they are making, and ensuring consistency in real-time collaboration.

**Primary Data Structure: `UserSession`**

* **Purpose**: To manage the state of each user's session while they are editing a document.  
* **Attributes**:  
  * `sessionId`: Unique identifier for the session.  
  * `userId`: The ID of the user.  
  * `documentId`: The ID of the document being edited.  
  * `isEditing`: A boolean to indicate whether the user is currently making changes.  
  * `lastActive`: Timestamp of the last activity to track session timeouts.
 


```
   struct UserSession {  
    int sessionId;  
    int userId;  
    int documentId;  
    bool isEditing;  
    string lastActive;  
  };
```
**Explanation**: The `UserSession` structure keeps track of important information for each user’s session. This includes whether the user is actively editing the document and when they were last active. The `lastActive` field helps in managing session timeouts, ensuring that if a user becomes inactive, their session can be appropriately handled (e.g., by saving their work or notifying other collaborators).

**Secondary Data Structure: `SessionManager`**

* **Purpose**: To manage all active sessions.  
* **Attributes**:  
  * `sessions`: A map or hash table where the key is the `sessionId`, and the value is the `UserSession` object.

```
struct SessionManager {  
    map<int, UserSession> sessions;  
};
```
**Explanation**: The `SessionManager` structure is responsible for handling all active sessions. By using a map, we can quickly access any session by its `sessionId`. This data structure allows us to efficiently manage multiple users editing the same document simultaneously, ensuring that all their changes are tracked and conflicts are resolved.

### **Communication Protocol Between Clients and the Server**

#### **Overview**

The Real-Time Collaborative Document Editing System requires a robust communication protocol to ensure that multiple users can edit documents simultaneously with high consistency and responsiveness. The protocol leverages WebSocket connections to maintain real-time, bi-directional communication between clients and the server.

#### **Key Components**

1. **Client-Server Architecture**  
   * **Client**: The web application running in the user's browser. It captures user actions (such as text insertion, deletion, or cursor movement) and sends these actions to the server.  
   * **Server**: The server processes incoming actions from clients, updates the document state, and broadcasts these changes back to all connected clients.  
2. **WebSockets for Real-Time Communication**  
   * **Persistent Connection**: WebSockets provide a continuous connection, enabling instant communication between the client and server.  
   * **Event Handling**: User actions are transmitted as events (e.g., `textInsert`, `textDelete`) from the client to the server. The server then processes these events and broadcasts updates to other clients to maintain document synchronization.

#### **Steps in the Communication Process**

1. **Connection Establishment**  
   * The client initiates a WebSocket connection with the server upon opening a document. This connection remains active throughout the session, allowing real-time updates.

**Event Handling**

* **Client-Side**: When a user makes an edit, the client sends the corresponding event to the server via WebSocket.  
* **Server-Side**: The server processes the event, updates the document state, and broadcasts the update to all other clients.

**Synchronization**

* **Broadcasting**: After processing an event, the server broadcasts the updated document state to all connected clients.  
* **Conflict Resolution**: If conflicting changes occur, the server resolves these conflicts using predefined rules or operational transformation techniques.

**Disconnection and Reconnection**

* If the WebSocket connection is lost, the client attempts to reconnect automatically. Upon reconnection, the server ensures the client receives the latest document state.

#### **Error Handling**

1. **Event Processing Failures**  
   * If the server fails to process an event, it sends an error message back to the client, which then handles the error appropriately.  
2. **Connection Failures**  
   * The client should automatically attempt to reconnect if the WebSocket connection is lost.

**Sequence Diagram**

![Screenshot 2024-08-16 141654](https://github.com/user-attachments/assets/81e6ac48-2eaf-452a-a056-7f238ac0b8d3)

**Class Diagram**:

![Screenshot 2024-08-16 142015](https://github.com/user-attachments/assets/36503718-6a08-4546-b832-3b5599b64c30)

### **How Multi-Threading Can Improve a Document Editing System**

**1\. Enhanced Performance:**

* **Parallel Processing:** Multi-threading enables parallel processing of different tasks. For instance, while one thread handles user input, another can handle document rendering or network communication. This leads to smoother and faster document editing.  
* **Efficient Resource Utilization:** By utilizing multiple CPU cores, multi-threading ensures that the system makes optimal use of available resources, reducing the time needed to process complex operations.

**2\. Improved Responsiveness:**

* **Non-Blocking UI:** Multi-threading allows for non-blocking user interfaces. For example, a separate thread can handle background tasks like saving changes or syncing with the server, preventing the UI from freezing or becoming unresponsive.  
* **Real-Time Updates:** In a collaborative environment, multi-threading can manage real-time updates efficiently. While one thread processes incoming changes from other users, another thread can update the local document view.

**3\. Enhanced Collaboration:**

* **Concurrency Control:** Multi-threading supports concurrent editing by multiple users. Threads can manage and resolve conflicts arising from simultaneous edits, ensuring that the document remains consistent and up-to-date.  
* **Background Synchronization:** Threads can handle background synchronization tasks, such as merging changes from different users or periodically saving drafts, ensuring that collaboration is seamless and continuous.

**4\. Scalability:**

* **Handling Multiple Users:** As the number of users increases, multi-threading can scale the system’s ability to handle multiple simultaneous actions. Each user’s actions can be processed in separate threads, improving the system's overall scalability.

**Class Diagram**:

![Screenshot 2024-08-16 142447](https://github.com/user-attachments/assets/c9d2fb84-6673-4108-a18b-48ba1d4a3518)

**Sequence Diagram**

![Screenshot 2024-08-16 142548](https://github.com/user-attachments/assets/453f0655-7a17-428f-8c4c-30fdf1890c57)

### **High-Level Approach to Rendering Changes from Multiple Users**

**1\. Overview:** In a collaborative document editing application, multiple users may make changes simultaneously. The client-side application needs to efficiently handle and display these changes in real-time so that all users see an updated and consistent version of the document.

**2\. Key Components:**

**2.1. Real-Time Updates:**

* **WebSocket Connection:** The application uses WebSocket to receive real-time updates from the server whenever other users make changes.

**2.2. Handling Changes:**

* **Change Processing:** When an update arrives, it needs to be processed and integrated into the local version of the document.

**2.3. Updating the User Interface:**

* **Rendering Changes:** The local document state is updated to include the new changes, and the user interface is refreshed to reflect these updates.

**3\. Step-by-Step Process:**

**3.1. Receive Updates:**

* **WebSocket Listener:** The application listens for updates from the server via WebSocket. When a change is made by another user, the server sends a message with details about the change (e.g., text added or removed).

**3.2. Apply Changes:**

* **Update Handling:** Once the update is received, the application applies these changes to the local document. This might involve inserting text at a specific position or deleting existing text.

**3.3. Refresh the View:**

* **Re-rendering:** After applying the changes, the application updates the display so that the user sees the most current version of the document.

**4\. Example:**

* **Receiving an Update:**  
  * User A types “Hello” at the beginning of the document.  
  * The server sends an update message to all users: “Insert ‘Hello’ at position 0.”  
* **Applying the Update:**  
  * The local document on User B’s screen is updated to insert “Hello” at the start.  
* **Refreshing the Display:**  
  * The user interface (e.g., the text area) is refreshed to show the updated document with “Hello” now visible.

 ![Screenshot 2024-08-16 143110](https://github.com/user-attachments/assets/574e65fe-1990-4c49-8a5a-69044b943a04)

### **Algorithm Implementation for Consistency**

**1\. Overview:** In a real-time collaborative document editing system, ensuring consistency among simultaneous users is critical. The system must handle concurrent operations (e.g., inserts, deletes) and merge changes without causing conflicts. The following algorithm outlines how to achieve this using Operational Transformation (OT) or Conflict-Free Replicated Data Types (CRDTs).

**2\. Key Components:**

* **Operation Queue:** A queue to hold incoming operations from users before they are processed and applied.  
* **Operation Transformation (OT):** An algorithm to transform operations to ensure consistency and resolve conflicts.  
* **Conflict Resolver:** A component to handle conflicts and ensure that all users see the same final document state.

**3\. Algorithm Steps:**

**3.1. Operation Representation:** Each operation (insert, delete) is represented as a data structure containing:

* **Type:** The type of operation (insert, delete).  
* **Position:** The position in the document where the operation is applied.  
* **Content:** The content being inserted or deleted.  
* **Timestamp:** A timestamp or logical clock to order operations.

**3.2. Operation Transformation:** When a user performs an operation, it is broadcasted to all other users. The OT algorithm transforms operations based on the context of concurrent operations. The transformation process involves:

* **Receiving Operations:** The system receives operations from different users.  
* **Transforming Operations:** Operations are transformed to account for the changes made by other users. For example, if two users insert text at the same position, the OT algorithm determines how to adjust the positions of these inserts.  
* **Applying Transformed Operations:** The transformed operations are applied to the local document, ensuring that the final document state is consistent with all users.

**3.3. Conflict Resolution:** Conflicts arise when simultaneous operations affect the same document region. The conflict resolution process involves:

* **Detecting Conflicts:** The system identifies conflicting operations (e.g., two users deleting the same text).  
* **Resolving Conflicts:** The conflict resolver applies predefined rules or algorithms to resolve conflicts. For instance, last-write-wins or merging changes based on operation timestamps.  
* **Broadcasting Resolutions:** Resolved changes are broadcasted to all users to update their document views.

Example Algorithm (Operational Transformation): 
```
class Operation {  
public:  
    string type;        // "insert" or "delete"  
    int position;       // Position in the document  
    string content;     // Content to be inserted or deleted  
    int timestamp;      // Logical timestamp for ordering

    // Constructor  
    Operation(string t, int p, string c, int ts) : type(t), position(p), content(c), timestamp(ts) {}  
};

class OTAlgorithm {  
public:  
    void transform(Operation &op1, Operation &op2) {  
        // Example transformation logic  
        if (op1.position < op2.position) {  
            if (op1.type == "insert" && op2.type == "insert") {  
                // Adjust position of op2  
                op2.position += op1.content.size();  
            } else if (op1.type == "delete" && op2.type == "insert") {  
                // Adjust position of op2  
                op2.position -= min(op1.content.size(), op2.position);  
            }  
        } else if (op1.position == op2.position) {  
            if (op1.type == "insert" && op2.type == "delete") {  
                // Handle simultaneous insert and delete at the same position  
                // Example: prefer insert or delete based on timestamp  
                if (op1.timestamp > op2.timestamp) {  
                    // Prefer op1 (insert)  
                } else {  
                    // Prefer op2 (delete)  
                }  
            }  
        }  
    }

    void applyOperation(Operation \&op) {  
        // Apply operation to the document  
        // Example: Insert content or delete content at the specified position  
    }  
};
```

### **Conflict Detection and Resolution**

**1\. Overview:** In a real-time collaborative document editing system, conflicts occur when multiple users perform operations (e.g., inserts, deletes) on the same document region simultaneously. Automatic conflict detection and resolution are essential for maintaining consistency and ensuring that all users see a coherent document state.

**2\. Conflict Detection:**

**2.1. Types of Conflicts:**

* **Operational Conflicts:** Occur when operations affect the same part of the document. For example, two users might insert text at the same position or one user might delete text that another user is trying to insert.  
* **State Conflicts:** Arise when the document's state is inconsistent due to concurrent updates. This can happen if operations are not applied in the correct order.

**2.2. Conflict Detection Mechanisms:**

* **Timestamp-Based Detection:** Use timestamps or logical clocks to order operations. Conflicts are detected if two operations are applied to the same position or region with overlapping timestamps.  
* **Version Vectors:** Track the version of the document state for each user. Conflicts are detected when users’ version vectors diverge due to simultaneous operations.  
* **Operational Transformation (OT) Algorithm:** In OT, conflicts are detected when operations overlap or affect the same document regions. The OT algorithm transforms and resolves these conflicts automatically.

**3\. Conflict Resolution:**

**3.1. Resolution Strategies:**

* **Last-Write-Wins:** The most recent operation (based on timestamp) is preferred, and earlier conflicting operations are overridden.  
* **Operational Transformation:** Adjusts the positions and effects of operations to account for concurrent changes. This involves transforming operations to be applied in a consistent manner, considering all concurrent changes.  
* **Merge Strategies:** Automatically merge changes where possible, such as combining insertions or deletions if they do not conflict directly.

**3.2. Conflict Resolution Process:**

1. **Detect Conflicts:** Identify conflicting operations based on timestamps, version vectors, or overlapping document regions.  
2. **Apply Transformation:** Use the OT algorithm or other transformation techniques to adjust the conflicting operations.  
3. **Resolve Conflicts:** Apply the transformed operations to the document. For example, if two users insert text at the same position, adjust the positions of the inserts to maintain the correct order.  
4. **Broadcast Resolutions:** Update all users with the resolved changes to ensure that everyone’s document view is consistent.  
5. **Verify Consistency:** Periodically verify that the document state is consistent across all users and that no unresolved conflicts remain.

**4\. Example:**

**Conflict Detection Example:**

* User A inserts "Hello" at position 5\.  
* User B deletes text from position 4 to 6\.  
* Conflict is detected because the insert and delete operations affect overlapping regions.

**Conflict Resolution Example (Using OT):**

* Transform the insert operation to account for the delete operation.  
  * Insert operation position adjusted based on the delete operation's effect.  
* Apply the transformed operations to the document:  
  * The text insertion is adjusted to avoid the deleted region.  
* Broadcast the resolved document state to all users.

![Screenshot 2024-08-16 145027](https://github.com/user-attachments/assets/b83ad56d-023e-4d4d-8707-6d19f9873174)
