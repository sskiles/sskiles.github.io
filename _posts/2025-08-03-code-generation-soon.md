---
layout: post
author: Shane Skiles
title: Code Generation Soon
tags: [c#, code generation, iincrementalgenerator]
---

Here's a fun little idea for a code generator. I've had this floating around 
in my head for a while. This would be a generator that reads SQL Server stored procedure 
parameter information and return information and generates C# classes for it.

This is the gist of it:
[ClassOMatic](https://gist.github.com/sskiles/ec7df45b80b4af28ce8c60da0736418b)

I know, it needs a better name and a little more polish, but it should work. 
I just can't get it working with `IIncrementalGenerator` and
`netstandard2.0`. Anyway...
It reads the stored procedure information from the database to get the parameters,
executes the stored procedure with null parameters to pull back the schema, 
then generates a result class, criteria class, and a repository class for it.

The repository class will return a populated `SqlCommand` based on the criteria object.
You populate the connection, execute the command and pass the `SqlDataReader` back
and get a populated result set. 

Here is an example workflow for stored procedure `GetPerson`:
---
layout: post
author: Shane Skiles
title: Code Generation Soon
tags: [c#, code generation, IIncrementalGenerator]
---

Here's a fun little idea for a code generator. I've had this floating around 
in my head for a while. This would be a generator that reads SQL Server stored procedure 
parameter and return information and then generates C# classes for it.

This is the gist of it:
[ClassOMatic](https://gist.github.com/sskiles/ec7df45b80b4af28ce8c60da0736418b)

I know, it needs a better name and a little more polish, but it should work. 
I just can't get it working with `IIncrementalGenerator` and
`netstandard2.0`. Anyway...
It reads the stored procedure information from the database to get the parameters,
executes the stored procedure with null parameters to retrieve the schema, 
then generates a result class, a criteria class, and a repository class for it.

The repository class will return a populated `SqlCommand` based on the criteria object.
You populate the connection, execute the command, pass the `SqlDataReader` back,
and get a populated result set. 

Here is an example workflow for the `GetPerson` stored procedure:
```csharp
// 1. Populate Criteria
GetPersonCriteria criteria = dboRepository.GetPersonCriteria()
				{
					Id = 1001,
					City = "New York",
					...
				}

// 2. Get Command
SqlCommand cmd = GetPersonRepository.CreateCommand(criteria)

// 3. Populate and Execute Command

// 4. 
Enumerable<GetPerson> results = GetPersonRepository.ReadAsync(sdr, cancellationToken);
```
Step 1 is to populate the criteria object. This is a simple record with the parameters
that the stored procedure expects. This object could be generated anywhere in the 
request pipeline. Pass it around, validate it, use it as a key in a cache, etc.

Step 2 is to create the command using the repository class. This is where the actual 
SQL command is constructed based on the criteria object. The procedure is set, 
the command type is set, and the parameters are added to the command. 

Step 3 is to populate the connection and execute the command. 
This is where the user would set the connection and any connection properties 
needed, such as a transaction or command timeout, before calling `ExecuteReaderAsync`.
The generated code never even touches the connection; that's up to the user.

Step 4 is to read the results. This is where the user would pass the `SqlDataReader` 
back to the repository class to read the results into a collection of the
result class. The generated code handles all the mapping—it already knows
the types and names of the columns in the result set, so it can map them directly 
without lookups.

As I mentioned, I haven't gotten it working with `IIncrementalGenerator` and probably won't
for a while, but I wanted to share the idea. In general, code generators shouldn't
have arbitrary I/O (like database access) in them, but if I ever get it working,
it will have its own "stupid c# trick" entry.

Need a new column? Add it to the stored procedure, run the generator, and you're done!
Need a new parameter? Need to change a parameter type? Same thing.

I'll revisit the idea again later, but I just wanted to share it now. 
I don't need it now, so I'll put it away for a while.
If you have any ideas or suggestions, feel free to reach out.
