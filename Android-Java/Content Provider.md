#What is Content Provider
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

Content providers let you centralize content in one place and have many different applications access it as needed. A content provider behaves very much like a database where you can query it, edit its content, as well as add or delete content using insert(), update(), delete(), and query() methods. In most cases this data is stored in an SQlite database.

A content provider is implemented as a subclass of ContentProvider class and must implement a standard set of APIs that enable other applications to perform transactions.

```
public class My Application extends  ContentProvider {

}
```

#Content URIs
To query a content provider, you specify the query string in the form of a URI which has following format:   
```
<prefix>://<authority>/<data_type>/<id>
```
- **prefix**: This is always set to content://
- **authority**: This specifies the name of the content provider, for example contacts, browser etc. For third-party content providers, this could be the fully qualified name, such as com.tutorialspoint.statusprovider  
- **data_type**: This indicates the type of data that this particular provider provides. For example, if you are getting all the contacts from the Contacts content provider, then the data path would be people and URI would look like this content://contacts/people
- **id**: This specifies the specific record requested. For example, if you are looking for contact number 5 in the Contacts content provider then URI would look like this content://contacts/people/5.  

#Create Content Provider
This involves number of simple steps to create your own content provider.    
- First of all you need to create a Content Provider class that extends the ContentProviderbaseclass.

- Second, you need to define your content provider URI address which will be used to access the content.

- Next you will need to create your own database to keep the content. Usually, Android uses SQLite database and framework needs to override onCreate() method which will use SQLite Open Helper method to create or open the provider's database. When your application is launched, the onCreate() handler of each of its Content Providers is called on the main application thread.

- Next you will have to implement Content Provider queries to perform different database specific operations.

- Finally register your Content Provider in your activity file using <provider> tag.

#Example
```
package com.example.My Application;

import android.net.Uri;
import android.os.Bundle;
import android.app.Activity;
import android.content.ContentValues;
import android.content.CursorLoader;
import android.database.Cursor;
import android.view.Menu;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends Activity {

   @Override
   protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
   }

   @Override
   public boolean onCreateOptionsMenu(Menu menu) {
      getMenuInflater().inflate(R.menu.main, menu);
      return true;
   }

   public void onClickAddName(View view) {
      // Add a new student record
      ContentValues values = new ContentValues();

      values.put(StudentsProvider.NAME, 
      ((EditText)findViewById(R.id.editText2)).getText().toString());
      
      values.put(StudentsProvider.GRADE, 
      ((EditText)findViewById(R.id.editText3)).getText().toString());

      Uri uri = getContentResolver().insert(
      StudentsProvider.CONTENT_URI, values);
      
      Toast.makeText(getBaseContext(), 
      uri.toString(), Toast.LENGTH_LONG).show();
   }

   public void onClickRetrieveStudents(View view) {
      
      // Retrieve student records
      String URL = "content://com.example.provider.College/students";
      
      Uri students = Uri.parse(URL);
      Cursor c = managedQuery(students, null, null, null, "name");
      
      if (c.moveToFirst()) {
         do{
            Toast.makeText(this, 
            c.getString(c.getColumnIndex(StudentsProvider._ID)) + 
            ", " +  c.getString(c.getColumnIndex( StudentsProvider.NAME)) + 
            ", " + c.getString(c.getColumnIndex( StudentsProvider.GRADE)), 
            Toast.LENGTH_SHORT).show();
         } while (c.moveToNext());
      }
   }
}
```

```
package com.example.My Application;

import java.util.HashMap;

import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.Context;
import android.content.UriMatcher;

import android.database.Cursor;
import android.database.SQLException;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteQueryBuilder;

import android.net.Uri;
import android.text.TextUtils;

public class StudentsProvider extends ContentProvider {

   static final String PROVIDER_NAME = "com.example.provider.College";
   static final String URL = "content://" + PROVIDER_NAME + "/students";
   static final Uri CONTENT_URI = Uri.parse(URL);

   static final String _ID = "_id";
   static final String NAME = "name";
   static final String GRADE = "grade";

   private static HashMap<String, String> STUDENTS_PROJECTION_MAP;

   static final int STUDENTS = 1;
   static final int STUDENT_ID = 2;

   static final UriMatcher uriMatcher;
   static{
      uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
      uriMatcher.addURI(PROVIDER_NAME, "students", STUDENTS);
      uriMatcher.addURI(PROVIDER_NAME, "students/#", STUDENT_ID);
   }

   /**
   * Database specific constant declarations
   */
   private SQLiteDatabase db;
   static final String DATABASE_NAME = "College";
   static final String STUDENTS_TABLE_NAME = "students";
   static final int DATABASE_VERSION = 1;
   static final String CREATE_DB_TABLE = 
   " CREATE TABLE " + STUDENTS_TABLE_NAME +
   " (_id INTEGER PRIMARY KEY AUTOINCREMENT, " + 
   " name TEXT NOT NULL, " +
   " grade TEXT NOT NULL);";

   /**
   * Helper class that actually creates and manages 
   * the provider's underlying data repository.
   */
   private static class DatabaseHelper extends SQLiteOpenHelper {
      DatabaseHelper(Context context){
         super(context, DATABASE_NAME, null, DATABASE_VERSION);
      }
      
      @Override
      public void onCreate(SQLiteDatabase db)
      {
         db.execSQL(CREATE_DB_TABLE);
      }
      
      @Override
      public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
         db.execSQL("DROP TABLE IF EXISTS " +  STUDENTS_TABLE_NAME);
         onCreate(db);
      }
   }
   
   @Override
   public boolean onCreate() {
      Context context = getContext();
      DatabaseHelper dbHelper = new DatabaseHelper(context);
   
      /**
      * Create a write able database which will trigger its 
      * creation if it doesn't already exist.
      */
      db = dbHelper.getWritableDatabase();
      return (db == null)? false:true;
   }

   @Override
   public Uri insert(Uri uri, ContentValues values) {
      /**
      * Add a new student record
      */
      long rowID = db.insert(	STUDENTS_TABLE_NAME, "", values);
      
      /** 
      * If record is added successfully
      */
      
      if (rowID > 0)
      {
         Uri _uri = ContentUris.withAppendedId(CONTENT_URI, rowID);
         getContext().getContentResolver().notifyChange(_uri, null);
         return _uri;
      }
      throw new SQLException("Failed to add a record into " + uri);
   }
   
   @Override
   public Cursor query(Uri uri, String[] projection, String selection,String[] selectionArgs, String sortOrder) {
      SQLiteQueryBuilder qb = new SQLiteQueryBuilder();
      qb.setTables(STUDENTS_TABLE_NAME);
      
      switch (uriMatcher.match(uri)) {
         case STUDENTS:
         qb.setProjectionMap(STUDENTS_PROJECTION_MAP);
         break;
         
         case STUDENT_ID:
         qb.appendWhere( _ID + "=" + uri.getPathSegments().get(1));
         break;
         
         default:
         throw new IllegalArgumentException("Unknown URI " + uri);
      }
      
      if (sortOrder == null || sortOrder == ""){
         /** 
         * By default sort on student names
         */
         sortOrder = NAME;
      }
      Cursor c = qb.query(db,	projection,	selection, selectionArgs,null, null, sortOrder);
      
      /**
      * register to watch a content URI for changes
      */
      c.setNotificationUri(getContext().getContentResolver(), uri);
      return c;
   }

   @Override
   public int delete(Uri uri, String selection, String[] selectionArgs) {
      int count = 0;
      
      switch (uriMatcher.match(uri)){
         case STUDENTS:
         count = db.delete(STUDENTS_TABLE_NAME, selection, selectionArgs);
         break;
         
         case STUDENT_ID:
         String id = uri.getPathSegments().get(1);
         count = db.delete( STUDENTS_TABLE_NAME, _ID +  " = " + id + 
         (!TextUtils.isEmpty(selection) ? " AND (" + selection + ')' : ""), selectionArgs);
         break;
         
         default: 
         throw new IllegalArgumentException("Unknown URI " + uri);
      }
      
      getContext().getContentResolver().notifyChange(uri, null);
      return count;
   }
   
   @Override
   public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs) {
      int count = 0;
      
      switch (uriMatcher.match(uri)){
         case STUDENTS:
         count = db.update(STUDENTS_TABLE_NAME, values, selection, selectionArgs);
         break;
         
         case STUDENT_ID:
         count = db.update(STUDENTS_TABLE_NAME, values, _ID + " = " + uri.getPathSegments().get(1) + 
         (!TextUtils.isEmpty(selection) ? " AND (" +selection + ')' : ""), selectionArgs);
         break;
         
         default: 
         throw new IllegalArgumentException("Unknown URI " + uri );
      }
      getContext().getContentResolver().notifyChange(uri, null);
      return count;
   }

   @Override
   public String getType(Uri uri) {
      switch (uriMatcher.match(uri)){
         /**
         * Get all student records 
         */
         case STUDENTS:
         return "vnd.android.cursor.dir/vnd.example.students";
         
         /** 
         * Get a particular student
         */
         case STUDENT_ID:
         return "vnd.android.cursor.item/vnd.example.students";
         
         default:
         throw new IllegalArgumentException("Unsupported URI: " + uri);
     }
  }
}
```

#Accessing a provider

An application accesses the data from a content provider with a ContentResolver client object. This object has methods that call identically-named methods in the provider object, an instance of one of the concrete subclasses of ContentProvider. The ContentResolver methods provide the basic "CRUD" (create, retrieve, update, and delete) functions of persistent storage.

The ContentResolver object in the client application's process and the ContentProvider object in the application that owns the provider automatically handle inter-process communication. ContentProvider also acts as an abstraction layer between its repository of data and the external appearance of data as tables.

For example, to get a list of the words and their locales from the User Dictionary Provider, you call ContentResolver.query(). The query() method calls the ContentProvider.query() method defined by the User Dictionary Provider. The following lines of code show a ContentResolver.query() call:    

```
// Queries the user dictionary and returns results
mCursor = getContentResolver().query(
    UserDictionary.Words.CONTENT_URI,   // The content URI of the words table
    mProjection,                        // The columns to return for each row
    mSelectionClause                    // Selection criteria
    mSelectionArgs,                     // Selection criteria
    mSortOrder);                        // The sort order for the returned rows
```

Table 2 shows how the arguments to query(Uri,projection,selection,selectionArgs,sortOrder) match an SQL SELECT statement:     

![Content Provider Query](/images/contentProviderQuery.png)
