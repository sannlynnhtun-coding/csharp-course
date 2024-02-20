# C# Course

- Db Connectors
  - [Ado.Net](#Ado.Net)
  - [Dapper](#Dapper)
  - [EF Core](#EFCore)
  
- ASP.Net Core Web API (REST/RESTful API)
  - [Rest Api with Ado.Net](#)
  - [Rest Api with Dapper](#)
  - [Rest Api with Ef Core](#)
  - [Rest Api with Postman](#)
 
- Console Application (Client) connect with ASP.Net Core Web API (Server)
   - [HttpClient](#HttpClient)
   - [RestClient](#)
   - [Refit](#)
 
- ASP.NET Core Web MVC
  -  [Ado.Net](#)
  -  [Dapper](#)
  -  [EF Core](#)

- ASP.NET Core Web MVC (Client) connect with ASP.Net Core Web API (Server)
  - [HttpClient](#)
  - [RestClient](#)
  - [Refit](#)
    
- ASP.NET Core Web Minimal API (like Express.js)
  - [Minimal Api](#)
  
- Text Logging With Serilog
   - [Conosole App](#)
   - [Web App](#)

 - Db Logging With Serilog
   - [Conosole App](#)
   - [Web App](#)
  
  - Chart
    - [ApexChart](#)
    - [ChartJs](#)
    - [HighCharts](#)
    - [CanvasJS](#)
   
  - SignalR (Websocket)
     - [Realtime Chart](#)
     - [Realtime Chat](#)

  - UI Desgin

  - Blazor CRUD [Server, WASM]

  - Deploy WASM

  - Deploy on IIS

  - Middleware For MVC

  - GraphQL

  - gRPC
  
#### DataSet
> Dataset is the local copy of your database which exists in the local system and makes the application execute faster and reliable. DataSet works like a real database with an entire set of data which includes the constraints, relationship among tables, and so on. It will be found in the namespace “System. Data”.

#### DataTable
> DataTable is a fundamental part of the ADO.NET framework, which provides a way to work with structured tabular data in memory. It represents an in-memory, disconnected table of data, similar to a database table or spreadsheet. The DataTable class is part of the System.Data namespace.

#### DataRow
> DataRow is a fundamental component of the ADO.NET framework, specifically associated with the DataTable class. A DataRow represents a single row of data within a DataTable. Each row corresponds to a single record in the table, and it contains values for each of the columns defined in the DataTable. You can manipulate, access, and modify the data within a DataRow.

#### DataColumn
> DataColumn is another important component of the ADO.NET framework, specifically associated with the DataTable class. A DataColumn represents a single column within a DataTable. Each DataColumn has a name, data type, and other properties that define how data is stored and validated within that column.

In C#, ADO.NET provides a robust framework for performing CRUD (Create, Read, Update, Delete) operations on a relational database. Here's a summary of how you can use ADO.NET to implement CRUD operations:

1. **Create (INSERT)**:
   - To insert data into a database using ADO.NET, establish a connection, create an INSERT command with parameters, associate it with a SqlConnection, set values, and execute it using ExecuteNonQuery().

2. **Read (SELECT)**:
   - To get data from a database with ADO.NET, connect to the database, create a SQL SELECT command associated with a SqlCommand object, execute it using SqlDataReader or SqlDataAdapter, and then process the retrieved data.

3. **Update (UPDATE)**:
   - To update data in a database using ADO.NET, connect to the database, create an SQL UPDATE command, link it to a `SqlCommand` object, set parameters for the update, and execute the command with `ExecuteNonQuery()` to make the changes in the database.

4. **Delete (DELETE)**:
   - To delete data from a database using ADO.NET, connect to the database, create an SQL DELETE command with a SqlCommand object, set any necessary parameters for deletion, and execute the command using ExecuteNonQuery() to remove the data.

5. **Error Handling and Exception Handling**:
   - Proper error handling is crucial in ADO.NET to manage exceptions that may occur during database operations. Use try-catch blocks to handle exceptions and ensure that resources are properly disposed of in the event of an error.

6. **Connection Management and Resource Cleanup**:
   - Always close and dispose of database connections, commands, and readers when you're finished with them to release resources and prevent memory leaks.

7. **Parameterization**:
   - Use parameterized queries to protect against SQL injection attacks and to safely pass data between your C# code and the database.

8. **Transaction Management**:
   - For more complex operations involving multiple database commands, consider using transactions to ensure that a series of commands are executed as a single unit, either all succeeding or all failing.

ADO.NET provides a flexible and powerful way to interact with databases in C#, allowing you to implement CRUD operations efficiently and securely in your applications.

## Ado.Net:

Open a terminal and install them with this command.
```csharp
dotnet add package System.Data.SqlClient
```
  
```csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Linq;
using System.Reflection.Metadata;
using System.Text;
using System.Threading.Tasks;

namespace csharp_course
{
    public class AdoDotNetExample
    {
        private readonly SqlConnectionStringBuilder sqlConnectionStringBuilder = new SqlConnectionStringBuilder
        {
            DataSource = ".", //server name (local)
            InitialCatalog = "TestDb", //db name
            UserID = "sa", //user name
            Password = "sa@123" //password
        };

        public void Run()
        {
            Read();
            Create("title1", "author1", "content1");
            Edit(2);
            Update(2, "title2", "author2", "content2");
            Delete(1);
        }

        private void Read()
        {
            SqlConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            connection.Open();

            string query = "select * from tbl_blog";
            SqlCommand cmd = new SqlCommand(query, connection);
            SqlDataAdapter adapter = new SqlDataAdapter(cmd);
            DataTable dt = new DataTable();
            adapter.Fill(dt);

            connection.Close();

            foreach (DataRow dr in dt.Rows)
            {
                Console.WriteLine($"Blog Id => {dr["BlogId"].ToString()}");
                Console.WriteLine($"Blog Title => {dr["BlogTitle"].ToString()}");
                Console.WriteLine($"Blog Author => {dr["BlogAuthor"].ToString()}");
                Console.WriteLine($"Blog Content => {dr["BlogContent"].ToString()}");
            }
        }

        private void Edit(int id)
        {
            SqlConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            connection.Open();

            string query = "select * from tbl_blog where BlogId=@BlogId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@BlogId", id);
            SqlDataAdapter adapter = new SqlDataAdapter(cmd);
            DataTable dt = new DataTable();
            adapter.Fill(dt);

            connection.Close();

            if(dt.Rows.Count == 0)
            {
                Console.WriteLine("No data found.");
                return;
            }

            DataRow dr = dt.Rows[0];
            Console.WriteLine($"Blog Id => {dr["BlogId"].ToString()}");
            Console.WriteLine($"Blog Title => {dr["BlogTitle"].ToString()}");
            Console.WriteLine($"Blog Author => {dr["BlogAuthor"].ToString()}");
            Console.WriteLine($"Blog Content => {dr["BlogContent"].ToString()}");
        }

        private void Create(string title, string author, string content)
        {
            SqlConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            connection.Open();

            string query = @"INSERT INTO [dbo].[Tbl_Blog]
           ([BlogTitle]
           ,[BlogAuthor]
           ,[BlogContent])
     VALUES
           (@BlogTitle
           ,@BlogAuthor
           ,@BlogContent)";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@BlogTitle", title);
            cmd.Parameters.AddWithValue("@BlogAuthor", author);
            cmd.Parameters.AddWithValue("@BlogContent", content);
            int result = cmd.ExecuteNonQuery();

            connection.Close();
            string message = result > 0 ? "Saving Successful.!" : "Saving Failed.";
            Console.WriteLine(message);
        }

        private void Update(int id, string title, string author, string content)
        {
            SqlConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            connection.Open();

            string query = @"UPDATE [dbo].[Tbl_Blog]
   SET [BlogTitle] = @BlogTitle
      ,[BlogAuthor] = @BlogAuthor
      ,[BlogContent] = @BlogContent
 WHERE BlogId = @BlogId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@BlogId", id);
            cmd.Parameters.AddWithValue("@BlogTitle", title);
            cmd.Parameters.AddWithValue("@BlogAuthor", author);
            cmd.Parameters.AddWithValue("@BlogContent", content);
            int result = cmd.ExecuteNonQuery();

            connection.Close();
            string message = result > 0 ? "Updating Successful.!" : "Updating Failed.";
            Console.WriteLine(message);
        }

        private void Delete(int id)
        {
            SqlConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            connection.Open();

            string query = "delete from tbl_blog where blogId = @BlogId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@BlogId", id);
            int result = cmd.ExecuteNonQuery();

            connection.Close();
            string message = result > 0 ? "Deleting Successful.!" : "Deleting Failed.";
            Console.WriteLine(message);
        }
    }
}
```
## Dapper:

> Dapper is an object–relational mapping product for the Microsoft .NET platform: it provides a framework for mapping an object-oriented domain model to a traditional relational database. Its purpose is to relieve the developer from a significant portion of relational data persistence-related programming tasks.

Open a terminal and install them with this command.
```csharp
dotnet add package Dapper
```
  
```csharp
using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using System.Data;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Dapper;
using MMYDotNetCore.ConsoleApp.Models;
using System.Reflection.Metadata;

namespace csharp_course
{
    public class DapperExample
    {
        private readonly SqlConnectionStringBuilder sqlConnectionStringBuilder = new SqlConnectionStringBuilder
        {
            DataSource = ".", //server name (local)
            InitialCatalog = "TestDb", //db name
            UserID = "sa", //user name
            Password = "sa@123" //sa@123
        };

        public void Run()
        {
            Read();
            Create("title1", "author1", "content1");
            Edit(2);
            Update(2, "title2", "author2", "content2");
            Delete(1);
        }

        public void Read()
        {
            using IDbConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            string query = "select * from tbl_blog";
            List<BlogModel> lst = connection.Query<BlogModel>(query).ToList();

            foreach (BlogModel item in lst)
            {
                Console.WriteLine($"Blog Id => {item.BlogId}");
                Console.WriteLine($"Blog Title => {item.BlogTitle}");
                Console.WriteLine($"Blog Author => {item.BlogAuthor}");
                Console.WriteLine($"Blog Content => {item.BlogContent}");
            }
        }

        public void Edit(int id)
        {
            string query = "select * from tbl_blog where BlogId=@BlogId";
            using IDbConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            var item = connection.Query<BlogModel>(query, new { BlogId = id }).FirstOrDefault();

            Console.WriteLine($"Blog Id => {item.BlogId}");
            Console.WriteLine($"Blog Title => {item.BlogTitle}");
            Console.WriteLine($"Blog Author => {item.BlogAuthor}");
            Console.WriteLine($"Blog Content => {item.BlogContent}");
        }

        public void Create(string title, string author, string content)
        {
            string query = @"INSERT INTO [dbo].[Tbl_Blog]
           ([BlogTitle]
           ,[BlogAuthor]
           ,[BlogContent])
     VALUES
           (@BlogTitle
           ,@BlogAuthor
           ,@BlogContent)";
            using IDbConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            int result = connection.Execute(query, new BlogModel
            {
                BlogTitle = title,
                BlogAuthor = author,
                BlogContent = content
            });

            string message = result > 0 ? "Saving Successful.!" : "Saving Failed.";
            Console.WriteLine(message);
        }

        public void Update(int id, string title, string author, string content)
        {
            string query = @"UPDATE [dbo].[Tbl_Blog]
   SET [BlogTitle] = @BlogTitle
      ,[BlogAuthor] = @BlogAuthor
      ,[BlogContent] = @BlogContent
 WHERE BlogId = @BlogId";
            using IDbConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            var result = connection.Execute(query, new BlogModel
            {
                BlogId = id,
                BlogTitle = title,
                BlogAuthor = author,
                BlogContent = content
            });
            string message = result > 0 ? "Updating Successful.!" : "Updating Failed.";
            Console.WriteLine(message);
        }

        public void Delete(int id)
        {
            string query = "delete from tbl_blog where blogId = @BlogId";
            using IDbConnection connection = new SqlConnection(sqlConnectionStringBuilder.ConnectionString);
            var result = connection.Execute(query, new
            {
                BlogId = id
            });
            string message = result > 0 ? "Deleting Successful.!" : "Deleting Failed.";
            Console.WriteLine(message);
        }
    }
}
```
## EFCore:

> Entity framework is an Object Relational Mapping (ORM) framework that gives developers an automated way to store and access databases.

Open a terminal and install them with this command.
```csharp
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer 
```

```csharp
using Microsoft.Data.SqlClient;
using Microsoft.EntityFrameworkCore.Storage;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace csharp_course
{
    public class EntityExample
    {
        public void Run()
        {
            Read();
            Edit(1);
            Create();
            Update(1);
            Delete(2);
        }
        private void Read()
        {
            var db = new AppDbContext();
            var lst = db.Blogs.ToList();
            var count = lst.Count();
            foreach (var item in lst)
            {
                Console.WriteLine(item.BlogId);
                Console.WriteLine(item.BlogTitle);
                Console.WriteLine(item.BlogAuthor);
                Console.WriteLine(item.BlogContent);
            }
        }

        private void Edit(int id)
        {
            var db = new AppDbContext();
            var item = db.Blogs.FirstOrDefault(x => x.BlogId == id);
            if (item == null)
            {
                Console.WriteLine("There is no data here");
            }
            Console.WriteLine(item.BlogId);
            Console.WriteLine(item.BlogTitle);
            Console.WriteLine(item.BlogAuthor);
            Console.WriteLine(item.BlogContent);
        }

        private void Create()
        {
            var db = new AppDbContext();
            var blogModel = new BlogModel()
            {
                BlogAuthor = "A",
                BlogContent = "A",
                BlogTitle = "A",
            };
            db.Blogs.Add(blogModel);
            var result = db.SaveChanges();
            var message = result > 0 ? "Saving Successful" : "Saving Unsuccessful";
            Console.WriteLine(message);
        }

        private void Update(int id)
        {
            var db = new AppDbContext();
            var item = db.Blogs.FirstOrDefault(x => x.BlogId == id);
            if (item == null)
            {
                Console.WriteLine("There is no data here");
            }
            item.BlogTitle = "new Title";
            item.BlogAuthor = "new Author";
            item.BlogContent = "new Content";
            var result = db.SaveChanges();
            var message = result > 0 ? "Update Successful" : "Update Unsuccessful";
            Console.WriteLine(message);
        }

        private void Delete(int id)
        {
            var db = new AppDbContext();
            var item = db.Blogs.FirstOrDefault(x => x.BlogId == id);
            if (item == null)
            {
                Console.WriteLine("There is no data here");
            }
            db.Blogs.Remove(item);
            var result = db.SaveChanges();
            var message = result > 0 ? "Delete Successful" : "Delete Unsuccessful";
            Console.WriteLine(message);
        }
    }
}
```
## HttpClient:
> HttpClient is a class in the . NET framework used for sending HTTP requests and receiving HTTP responses from a resource identified by a URI. It provides a convenient way to make     HTTP requests from . NET applications and can be used to perform various operations such as sending GET, POST, PUT, DELETE requests, etc.

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using static System.Net.Mime.MediaTypeNames;

namespace csharp_course
{
    public class HttpClienttExample
    {
        public async Task Run()
        {
            await Read(1, 5);
            await Read(2, 5);
            await Edit(1);

            await Create(new BlogModel
            {
                BlogAuthor = "test",
                BlogContent = "test",
                BlogTitle = "test",
            });
            await Delete(7);

            await Update(10, new BlogModel
            {
                BlogAuthor = "new test",
                BlogContent = "new test",
                BlogTitle = "new test",
            });

            await PatchUpdate(10, new BlogModel
            {
                BlogAuthor = " test",
                BlogContent = "new test",
                BlogTitle = "new test",
            });
        }

        // Read
        private async Task Read(int pageNo, int pageSize) //pageNo and pageSize is for pageSize
        {
            HttpClient client = new HttpClient();
            HttpResponseMessage response = await client.GetAsync($"https://localhost:44357/api/blog/{pageNo}/{pageSize}");
            if (response.IsSuccessStatusCode)
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                List<BlogModel> lst = JsonConvert.DeserializeObject<List<BlogModel>>(jsonStr); // json to c# object

                Console.WriteLine(JsonConvert.SerializeObject(lst, Formatting.Indented));
            }
        }

        // Edit
        private async Task Edit(int id)
        {
            HttpClient client = new HttpClient();
            HttpResponseMessage response = await client.GetAsync($"https://localhost:44357/api/blog/{id}");
            if (response.IsSuccessStatusCode)
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr); // json to c# object
                if (responseModel.IsSuccess)
                {
                    Console.WriteLine(responseModel.Message);
                    Console.WriteLine(JsonConvert.SerializeObject(responseModel.Data, Formatting.Indented));
                }
            }
            else
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr); // json to c# object
                Console.WriteLine(responseModel.Message);
            }
        }

        // Create
        private async Task Create(BlogModel blog)
        {
            HttpClient client = new HttpClient();

            var jsonBlog = JsonConvert.SerializeObject(blog);
            HttpContent content = new StringContent(jsonBlog, Encoding.UTF8, Application.Json);

            var response = await client.PostAsync("https://localhost:44357/api/blog", content);
            if (response.IsSuccessStatusCode)
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                //Console.WriteLine(jsonStr);
                BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr); // json to c# object
                if (responseModel.IsSuccess)
                {
                    Console.WriteLine(responseModel.Message);
                    Console.WriteLine(JsonConvert.SerializeObject(responseModel.Data, Formatting.Indented));
                }
            }
        }

        // Update
        private async Task Update(int id, BlogModel blog)
        {
            HttpClient client = new HttpClient();
            var jsonBlog = JsonConvert.SerializeObject(blog);
            HttpContent content = new StringContent(jsonBlog, Encoding.UTF8, Application.Json);
            var response = await client.PutAsync($"https://localhost:44357/api/blog/{id}", content);
            if (response.IsSuccessStatusCode)
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr);
                if (responseModel.IsSuccess)
                {
                    Console.WriteLine(responseModel.Message);
                    Console.WriteLine(JsonConvert.SerializeObject(responseModel.Data, Formatting.Indented));
                }
            }
        }

        // Patch
        private async Task PatchUpdate(int id, BlogModel blog)
        {
            HttpClient client = new HttpClient();
            var jsonBlog = JsonConvert.SerializeObject(blog);
            HttpContent content = new StringContent(jsonBlog, Encoding.UTF8, Application.Json);
            var response = await client.PatchAsync($"https://localhost:44357/api/blog/{id}", content);
            if (response.IsSuccessStatusCode)
            {
                var jsonStr = await response.Content.ReadAsStringAsync();
                BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr);
                if (responseModel.IsSuccess)
                {
                    Console.WriteLine(responseModel.Message);
                    Console.WriteLine(JsonConvert.SerializeObject(responseModel.Data, Formatting.Indented));
                }
            }
        }

        // Delete
        private async Task Delete(int id)
        {
            HttpClient client = new HttpClient();
            HttpResponseMessage response = await client.DeleteAsync($"https://localhost:44357/api/blog/{id}");

            var jsonStr = await response.Content.ReadAsStringAsync();
            BlogResponseModel responseModel = JsonConvert.DeserializeObject<BlogResponseModel>(jsonStr);

            Console.WriteLine(responseModel.Message);
        }
    }
}
```

