Content providers are one of the primary building blocks of Android applications, providing content to applications. They encapsulate data and provide it to applications through the single ContentResolver interface. A content provider is only required if you need to share data between multiple applications. For example, the contacts data is used by multiple applications and must be stored in a content provider. If you don't need to share data amongst multiple applications you can use a database directly via SQLiteDatabase.   

When a request is made via a ContentResolver the system inspects the authority of the given URI and passes the request to the content provider registered with the authority. The content provider can interpret the rest of the URI however it wants. The UriMatcher class is helpful for parsing URIs.

The primary methods that need to be implemented are:   
- onCreate() which is called to initialize the provider
- query(Uri, String[], String, String[], String) which returns data to the caller
- insert(Uri, ContentValues) which inserts new data into the content provider
- update(Uri, ContentValues, String, String[]) which updates existing data in the content provider
- delete(Uri, String, String[]) which deletes data from the content provider
- getType(Uri) which returns the MIME type of data in the content provider   

Requests to ContentResolver are automatically forwarded to the appropriate ContentProvider instance, so subclasses don't have to worry about the details of cross-process calls.