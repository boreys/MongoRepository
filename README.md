
[![Visual Studio Team services](https://img.shields.io/vso/build/dissperltd/6c214d4e-3676-4b7d-b7fb-9d7079975c67/7.svg?style=flat-square)]()
[![Version](https://img.shields.io/nuget/v/Repository.Mongo.svg?style=flat-square)](https://www.nuget.org/packages/Repository.Mongo)
[![Downloads](https://img.shields.io/nuget/dt/Repository.Mongo.svg?style=flat-square)](https://www.nuget.org/packages/Repository.Mongo)

## MongoRepository
Repository pattern for MongoDB with extended features

### Definition

#### Model
You don't need to create a model, but if you are doing so you need to extend Entity
```csharp
	//if you are able to define your model
	public class User : Entity
	{
		public string Username { get; set; }
		public string Password { get; set; }
	}
```

#### Repository
There are multiple base constructors, read summaries of others
```csharp
	public class UserRepository : Repository<User>
	{
		public UserRepository(string connectionString) : base(connectionString) {}

		//custom method
		public User FindbyUsername(string username)
		{
			return First(i => i.Username == username);
		}
		
		//custom method2
		public void UpdatePassword(User item, string newPassword)
		{
			repo.Update(item, i => i.Password, newPassword);
		}
	}
```

*If you want to create a repository for already defined non-entity model*
```csharp
	public class UserRepository : Repository<Entity<User>>
	{
		public UserRepository(string connectionString) : base(connectionString) {}

		//custom method
		public User FindbyUsername(string username)
		{
			return First(i => i.Content.Username == username);
		}
	}
```

### Usage

Each method has multiple overloads, read method summary for additional parameters

```csharp
	UserRepository repo = new UserRepository("mongodb://localhost/sample")

	//Get
	User user = repo.Get("58a18d16bc1e253bb80a67c9");

	//Insert
	User item = new User(){
		Username = "username",
		Password = "password"
	};
	repo.Insert(item);

	//Update
	//single property
	repo.Update(item, i => i.Username, "newUsername");

	//multiple property
	//Updater has many methods like Inc, Push, CurrentDate, etc.
	var update1 = Updater.Set(i => i.Username, "oldUsername");
	var update2 = Updater.Set(i => i.Password, "newPassword");
	repo.Update(item, update1, update2);

	//all entity
	item.Username = "someUsername";
	repo.Replace(item);

	//Delete
	repo.Delete(item);

	//Queries - all queries has filter, order and paging features
	var first = repo.First();
	var last = repo.Last();
	var search = repo.Find(i => i.Username == "username");
	var allItems = repo.FindAll();

	//Utils
	var count = repo.Count();
	var any = repo.Any(i => i.Username.Contains("user"));
```
