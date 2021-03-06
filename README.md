AlienDroid - ActiveRecord
=========================

Install
----------
IMPORTANT: Sorry, we don't have a final version yet. Maybe with your help, we can do that soon. :) Just help us with tests, suggestions, comments and so on. We publish our artifacts at Sonatype Nexus.

If you use Maven, just add these dependency:

	<dependency>
		<groupId>com.alienlabz</groupId>
		<artifactId>aliendroid-activerecord</artifactId>
		<version>1.0.0-SNAPSHOT</version>
	</dependency>

If you don't use Maven, just go to our Download section and get [aliendroid-core.jar](https://github.com/downloads/AlienLabZ/aliendroid-core/aliendroid-core-1.0.0-SNAPSHOT.jar) and [aliendroid-activerecord.jar](https://github.com/downloads/AlienLabZ/aliendroid-activerecord/aliendroid-activerecord-1.0.0-SNAPSHOT.jar). Add those jars to your dependencies in your Android project. You will need RoboGuice and his specifics dependencies too:

1. [RoboGuice 2 Download](http://repo1.maven.org/maven2/org/roboguice/roboguice/2.0/)
2. [Guice 3 No AOP](http://repo1.maven.org/maven2/com/google/inject/guice/3.0/guice-3.0-no_aop.jar)
3. [JSR 305](http://repo1.maven.org/maven2/com/google/code/findbugs/jsr305/1.3.9/jsr305-1.3.9.jar)
4. [javax.inject](http://repo1.maven.org/maven2/javax/inject/javax.inject/1/javax.inject-1.jar)

Using
------
You need initialize our engine. To do that, create one class that inherits from android.app.Application and execute AlienDroid.init(this) method:

	public class MyApplication extends android.app.Application {

		@Override
		public void onCreate() {
			super.onCreate();
			AlienDroid.init(this);
		}

	}

Remember to inform this class in your AndroidManifest.xml:

    <application
        android:name=".MyApplication"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name" >
	...

### Using AlienDroid!

Now, suppose that your project has only one class that you want to map to a table. Let's call this class "Contact":

	public class Contact extends Model {
		public String name;
		public Date birth;
		public Integer age;
	}
	
That's all you need. No annotations are required. Not even configurations. Now, you can simply do:

	public MyActivity extends RoboActivity {
	
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.main);
			
			Contact contact = new Contact();
			contact.name = "My Name";
			contact.birth = new Date();
			contact.age = 21;
			contact.save();
		}
	}

So, do you want to know what AlienDroid is doing behind the scenes?

1. Creating a database named "database.sqlite".
2. Creating one table per Model subclass.
3. Creating table columns based on your model attributes.

Queries
--------
To load data from database, just do:

	public MyActivity extends RoboActivity {
	
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.main);
			
			Contact contact = Model.load(Contact.class, 1);
			contact.name = "Changed Name";
			contact.save();
			
			List<Contact> contacts = Model.findAll(Contact.class);
			List<Contact> contacts = Model.where(Contact.class, "name like '?'", new String[] {"name"});
			
			contact.delete();
		}
	}
	
Changing Database Name
----------------------
If you don't like how we named your database file (database.sqlite), you can change it. Just add those lines to your AndroidManifest file (remember to put it inside <application> tag).

        <meta-data android:name="DATABASE_NAME" android:value="mydatabase.sqlite"/>
        <meta-data android:name="DATABASE_VERSION" android:value="1"/>
        
Events
-------
You can observes database events too. Suppose you want to apply some changes to your tables when database's version is changed. 


	public MyActivity extends RoboActivity {
	
		@Override
		public void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.main);
		}
		
		public void databaseUpgrade(@Observes DatabaseUpgrade event) {
			event.getDatabase();
		}
		
		public void databaseCreated(@Observes DatabaseCreated event) {
			event.getDatabase();
		}
	}


Relationships
--------------
AlienDroid ActiveRecord doesn't implement relationships. 