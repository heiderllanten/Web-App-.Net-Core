# Web-App-.Net-Core

## Razor

### Get Started

#### Pages folder

Contains Razor pages and supporting files. Each Razor page is a pair of files:

- .cshtml file that contains HTML markup with C# code using Razor syntax.
- .cshtml.cs file that contains C# code that handles page events.

#### wwwroot folder

Contains static files, such as HTML files, JavaScript files, and CSS files.

#### appSettings.json

Contains configuration data, such as connection strings.

#### Program.cs
Contains the entry point for the program.

#### Startup.cs
Contains code that configures app behavior.

### Add a Model

#### Add data model

- The ID field is required by the database for the primary key.

#### Scaffold the movie model

- Right click on the Pages/Movies folder > Add > New Scaffolded Item.
- In the Add Scaffold dialog, select Razor Pages using Entity Framework (CRUD) > Add.
- In the Model class drop down, select Movie (RazorPagesMovie.Models).
- In the Data context class row, select the + (plus) sign and change the generated name from RazorPagesMovie.Models.RazorPagesMovieContext to RazorPagesMovie.Data.RazorPagesMovieContext. This change is not required. It creates the database context class with the correct namespace.
- Select Add.

#### Initial Migration

- Add-Migration InitialCreate
- Update-Database

The migrations command generates code to create the initial database schema. The schema is based on the model specified in DbContext. The InitialCreate argument is used to name the migrations. Any name can be used, but by convention a name is selected that describes the migration.

The update command runs the Up method in migrations that have not been applied. In this case, update runs the Up method in Migrations/<time-stamp>_InitialCreate.cs file, which creates the database.

### Scaffolding

#### The @page directive

The @page Razor directive makes the file an MVC action, which means that it can handle requests. @page must be the first Razor directive on a page. @page is an example of transitioning into Razor-specific markup.

#### The @model directive

The @model directive specifies the type of the model passed to the Razor Page. In the preceding example, the @model line makes the PageModel-derived class available to the Razor Page. The model is used in the @Html.DisplayNameFor and @Html.DisplayFor HTML Helpers on the page.

#### The layout page

Layout templates allow the HTML container layout to be:

- Specified in one place.
- Applied in multiple pages in the site.

Find the @RenderBody() line. RenderBody is a placeholder where all the page-specific views show up, wrapped in the layout page.

#### ViewData and layout

The PageModel base class contains a ViewData dictionary property that can be used to pass data to a View. Objects are added to the ViewData dictionary using a key/value pattern.

### Work with a database

When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server. See Configuration for more information.

#### SQL Server Express LocalDB

LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development. LocalDB starts on demand and runs in user mode, so there's no complex configuration. By default, LocalDB database creates *.mdf files in the C:\Users\<user>\ directory

### Update the pages

#### Update the generated pages

Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.

#### Posting and binding review

When an HTTP GET request is made to the Movies/Edit page (for example, http://localhost:5000/Movies/Edit/2):

- The OnGetAsync method fetches the movie from the database and returns the Page method.
- The Page method renders the Pages/Movies/Edit.cshtml Razor Page. The Pages/Movies/Edit.cshtml file contains the model directive (@model RazorPagesMovie.Pages.Movies.EditModel), which makes the movie model available on the page.
- The Edit form is displayed with the values from the movie.

When the Movies/Edit page is posted:

- The form values on the page are bound to the Movie property. The [BindProperty] attribute enables Model binding.
- If there are errors in the model state (for example, ReleaseDate cannot be converted to a date), the form is redisplayed with the submitted values.
- If there are no model errors, the movie is saved.

### Add search

For security reasons, you must opt in to binding GET request data to page model properties. Verify user input before mapping it to properties. Opting into GET binding is useful when addressing scenarios that rely on query string or route values.

To bind a property on GET requests, set the [BindProperty] attribute's SupportsGet property to true:

The s => s.Title.Contains() code is a Lambda Expression. Lambdas are used in method-based LINQ queries as arguments to standard query operator methods such as the Where method or Contains (used in the preceding code). 

LINQ queries are not executed when they're defined or when they're modified by calling a method (such as Where, Contains or OrderBy). Rather, query execution is deferred. That means the evaluation of an expression is delayed until its realized value is iterated over or the ToListAsync method is called. 

The ASP.NET Core runtime uses model binding to set the value of the SearchString property from the query string (?searchString=Ghost) or route data (https://localhost:5001/Movies/Ghost). Model binding is not case sensitive.

The HTML <form> tag uses the following Tag Helpers:

- Form Tag Helper. When the form is submitted, the filter string is sent to the Pages/Movies/Index page via query string.
- Input Tag Helper