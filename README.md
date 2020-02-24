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

The HTML '<form>' tag uses the following Tag Helpers:

- Form Tag Helper. When the form is submitted, the filter string is sent to the Pages/Movies/Index page via query string.
- Input Tag Helper

### Add a new field

The SqlException exception is caused by the updated Movie model class being different than the schema of the Movie table of the database. (There's no Rating column in the database table.)

There are a few approaches to resolving the error:

* Have the Entity Framework automatically drop and re-create the database using the new model class schema. This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together. The downside is that you lose existing data in the database. Don't use this approach on a production database! Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.

* Explicitly modify the schema of the existing database so that it matches the model classes. The advantage of this approach is that you keep your data. You can make this change either manually or by creating a database change script.

* Use Code First Migrations to update the database schema.

From the Tools menu, select NuGet Package Manager > Package Manager Console. In the PMC, enter the following commands:

- Add-Migration Rating
- Update-Database

The Add-Migration command tells the framework to:

- Compare the Movie model with the Movie DB schema.
- Create code to migrate the DB schema to the new model.

The Update-Database command tells the framework to apply the schema changes to the database and to preserve existing data.

### Add validation

#### Add validation rules to the movie model

The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property. DataAnnotations also contains formatting attributes like DataType that help with formatting and don't provide any validation.

The validation attributes specify behavior that you want to enforce on the model properties they're applied to:

- The Required and MinimumLength attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.

- The StringLength attribute lets you set the maximum length of a string property, and optionally its minimum length.

- Value types (such as decimal, int, float, DateTime) are inherently required and don't need the [Required] attribute.

Having validation rules automatically enforced by ASP.NET Core helps make your app more robust. It also ensures that you can't forget to validate something and inadvertently let bad data into the database.

#### Validation Error UI in Razor Pages

Notice how the form has automatically rendered a validation error message in each field containing an invalid value. The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).

A significant benefit is that no code changes were necessary in the Create or Edit pages. Once DataAnnotations were applied to the model, the validation UI was enabled.

#### Server-side validation

The Create and Edit pages have no validation rules in them. The validation rules and the error strings are specified only in the Movie class. These validation rules are automatically applied to Razor Pages that edit the Movie model.

When validation logic needs to change, it's done only in the model. Validation is applied consistently throughout the application (validation logic is defined in one place). Validation in one place helps keep the code clean, and makes it easier to maintain and update.

#### Using DataType Attributes

The 'DataType' attributes only provide hints for the view engine to format the data (and supplies attributes such as '<a>' for URL's and '<a href="mailto:EmailAddress.com">' for email). Use the 'RegularExpression' attribute to validate the format of the data. The DataType attribute is used to specify a data type that's more specific than the database intrinsic type. DataType attributes are not validation attributes. In the sample application, only the date is displayed, without time.

The 'DataType' Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more. The DataType attribute can also enable the application to automatically provide type-specific features. For example, a 'mailto:' link can be created for 'DataType.EmailAddress'. A date selector can be provided for 'DataType.Date' in browsers that support HTML5. The DataType attributes emits HTML 5 'data-' (pronounced data dash) attributes that HTML 5 browsers consume. The 'DataType' attributes do not provide any validation.

The 'DisplayFormat' attribute can be used by itself, but it's generally a good idea to use the 'DataType' attribute. The 'DataType' attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:

- The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)
- By default, the browser will render data using the correct format based on your locale.
- The 'DataType' attribute can enable the ASP.NET Core framework to choose the right field template to render the data. The 'DisplayFormat' if used by itself uses the string template.