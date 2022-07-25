HIBERNATE
	- ORM Tool (Object Relational Mapping)
	- used in data layer of applications
	- implements JPA

Hibernate allows us to think/focus in terms of objects and avoid all the complexities involved for mapping the objects in to a relational database.

What is an ORM (Object Relational Mapping) Tool:
	- It solves the common problem of saving object from java to a persistent database against a table which generally represents the class schema.
	- It solves the common problem of:
		1. mapping member variables to columns
		2. mapping relationships
		3. handling data types
		4. managing changes to object state
		
Steps we generally have to perform without hibernate:
	- JDBC Database configuration
	- The Model(bean) object
	- Service method to create the model object
	- Database design
	- DAO methods to save the object using SQL queries
	
Steps with hibernate:
	- JDBC database configuration (hibernate configuration)
	- The Model (bean) object - Annotations
	- Service method to create the model object - use the hibernate API
	- Database design - Not Needed!
	- DAO methods to save the object using SQL queries - Not Needed!
	
What is a dialect?
	dialect is a configuration for hibernate to know what kind of language to use while talking to database.
	Even though we use SQL to talk to a dabase, but there are some nuiances which are specific to a database.
	The way we use queries in Oracle is different from the way we run queries in MySQL.
	if we specify the dialect, hibernate will then use that particular dialect to write sql queries.
	
Using Hibernate API
	1. create a session factory
		(only one object per application)
		creates sessions depending of no of sessions required
		we get a session from the session factory to do any operation in database
	
	2. get session from the session factory
	3. start transaction
	4. perform required db operations
	5. commit / abort transaction
	6. close session
	
Natural Key vs Surrogate Key:
	such columns which are unique as per business logic as called natural key.
	but if there is not any column or you are not sure about any column which can be marked as unique, then a separtae column (like serialNo.) just for uniqueness of rows. such keys/colums is called as surrogate key.
	
	
Entity Object vs Value Object:
	Entity is independent
	Entity cotains data which has a meaning on its own (like a User object)
	
	Value object is an object that has data which does not have meaning on its own, but provides meaning to othet object.
	Like Address object. An Address object if defined does not have a meaning until it is give whose address is it.
	
	
Lazy Fetch vs Eager Fetch:
	With Lazy Fetch type hibernate does not initialize or fetches value for the entire object. Only the first level member variables are fetched. And other variables like list or another entity is fetched only when that variable is accessed (i.e getter for that variable is called.)
	Lazy fetch is default behaviour of hibernate.
	
	To achieve this lazy fetch, hibernate creates a proxy class (dynamic sub-class of the actual object) which overrides some getter methods of the second level member variables, where the code for getting the value from db is written before calling the getter of actual class.
	
	
	With Eager fetch strategy hibernate pull entire object in one go.
	
	
hibernate configurations:
      <!-- SQL Dialect -->
      <property name="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</property>
      
      <!-- Database connection settings -->
      <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/DBPocHibernate</property>
      <property name="hibernate.connection.username">dbasys</property>
      <property name="hibernate.connection.password">oracle</property>
      
      <!-- (create/update) : drop existing and re-create database schema / update in existing schema -->
      <property name="hbm2ddl.auto">create</property>
      
	  <!-- show sql queries getting executed in console -->
      <property name="show_sql">true</property>
      
	  <!-- classes in which to look for hibernate annotations -->
      <mapping class="in.chandanpal.pochibernate.model.UserDetails"/>
	
	

Annotations:
@Entity : (javax.persistence) : tells hibernate to treat the class as an entity
@Entity(name="USER_DETAILS") : tells hibernate to create entity with name passed instead of the class name
@Id : (javax.persistence) : tells hibernate to treat the field as primary key
@Column : tells hibernate to create column corresponding to the field (it is optional, even if not provided, column will be created)
@Column(name="USER_NAME") : tells hibernate to create column with the name passed instead of the field name directly.
@Table(name="USER_DETAILS") : tells hibernate to create a table with the name passed.
@Transient : tell hibernate not to consider a field for persistence (hibernate also ignores fields which are transient or static variable)
@Temporal(TemporalType.DATE) : for date fields to use different formats of date
@Lob : large object (clob/blob) hibernate chooses a clob or blob depending on the type (byte array or String)
@GeneratedValue : tells hibernate to generate an automatic value for that field/column with default generation strategy
@GeneratedValue(strategy=GenerationType.SEQUENCE) : tells hibernate to generate an automatic value for that field/column with specific strategy
@Embeddable : tells hibernate that this class can be embedded in another entity
@Embedded : tells hibernate that this fields has to be embedded inside the parent entity (means all the fields of this object will become the field of the entity class)
@AttributeOverride : helps to re-configure all the fields of an embedded object
@EmbeddedId : if the embedded object has to act as a primary key (combination of all fields of the embedded object act as primary key for the current entity)
@ElementCollection : tells hibernate to treat the field as a collection (creates a separtae table to store the collection field)
@ElementCollection(fetch=FetchType.EAGER) : tells hibernate to treat the field as a collection with eager fetch strategy.
@JoinTable(name="user_address") : tells hibernate to use a custom name for collection or join tables
@JoinTable(joinColumns = @JoinColumn(name="userid")) : tells hibernate to use a custom name for foreign key in join table
@CollectionId(columns = { @Column }, generator = "", type = @Type) : (org.hibernate.annotations) not a persistence annotation but specific to hibernate
			tells hibernate to create a primary key column for a collections table
			e.g. @GenericGenerator(name="sequencegen", strategy="sequence")
			@CollectionId(columns = { @Column(name="address_id") }, generator = "sequencegen", type = @Type(type="long"))
			Note: to use @CollectionId use collection type which supports index, like list and set
@OneToOne  : on top of the field to create a one to one relationship, instead of embedding in the same table like collection
			In this case a column is added in one entity to map with the primary key of another entity
@OneToMany : on top of the field to create a one to many relationship, instead of embedding in the same table like collection
			In this case a seperate table is created to store the mapping between primary keys of both the tables
			configuring the join table-
			@JoinTable(name="user_vehicles", joinColumns=@JoinColumn(name="user_id"), inverseJoinColumns=@JoinColumn(name="vehicle_id"))
@ManyToOne : on top of the field to create a one to many relationship, instead of embedding in the same table like collection
			similar like one to many
			Note : for creating a mappedBy column for the many to one relationship, instead of creating a seperate mapping table - we have to add annotation @JoinColumn(name="columnName")
@ManyToMany : on top of the field to create a mamy to many relationship. To be added on both entity ends. Two mapping tables are created by default, one for each side. To stop hibernate from creating two mapping tables for same mapping use mappedBy property.
e.g @ManyToMany(mappedBy="<fieldNameOfOtherEntity>")
@NotFound(action=NotFoundAction.IGNORE) : (org.hibernate.annotations) - tells hibernate what to do if there is no data for that field while fetching. Otherwise it will through exception
@OneToMany(cascade = CascadeType.PERSIST) : tells hibernate to cascade the saving operation for the member entity also


Note: @Id, @Column can also added on top of getter method in place of on top of the field (In that case the values which are returned from the getter will be saved, and not the value of the field directly)


Hibernate Collections:
	1. Bag semantic (unordered) - List / ArrayList
	2. Bag semantic with ID (unordered with ID)- List / ArrayList
	3. List semantic (List can be ordered) - List / ArrayList
	4. Set semantic - Set
	5. Map semantic - Map
	
Cascading Operations:
	If one object has refernce to another object, then by default behaviour both the objects have to be operated individually then after that the relationship between both the objects is identified.
	
	e.g.
	If "user" object has reference to "vehicle" object. the both user object and vehicle object have to be saved separately
	User user = new User();
	Vehicle vehicle = new Vehicle();
	user.setVehicle(vehicle);
	session.save(user) - will not save the vehicle object automatically.
	session.save(vehicle) - this also needs to be done
	
	(Note: this is not the case for embedded objects, since vaule objects are stored in the same table)
	
	To tell hibernate to do this automatically we add "cascade" property on the relationship. This behaviour is called cascading. Cascading can be defined operation specific like PERSIST, DETACH, REMOVE, REFRESH, ALL etc.


CRUD operations in hibernate:
	session.save(<object>);
	session.get(<class>, <identifierValue>);
	session.update(<object>);
	session.delete(<object>);


Transient, persistent and detached object:
	Transient  : The entity object is instantiated, but not saved in the database. (i.e save is not called for that object)
	
	Persistent : The object is saved in the database (save for the object is called) - if save for an object is called, hibernate tracks the object for any changes in the object (even after save has been called), and updates the changes in database also
	Note: update is not called for every change in the object state, but it figures out intelligently to call uodate only for last change in the object state.
	
	Detached   : Once the session is closed, hibernate stops tracking for any changes in the object, and the object is called detached object


