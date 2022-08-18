# csharp-web-scraping
Web Scraping With C#

```bash
dotnet --version
```

```bash
dotnet new console
```

```bash
dotnet add package HtmlAgilityPack
```

```bash
dotnet add package CsvHelper
```

```csharp
// Parses the URL and returns HtmlDocument object
static HtmlDocument GetDocument(string url)
{
    HtmlWeb web = new HtmlWeb();
    HtmlDocument doc = web.Load(url);
    return doc;
}
```

```csharp
public HtmlNodeCollection SelectNodes(string xpath);
```

```csharp
public HtmlNode SelectSingleNode(string xpath);
```

```css
//h3/a
```

```csharp
HtmlDocument doc = GetDocument(url);
HtmlNodeCollection linkNodes = doc.DocumentNode.SelectNodes("//h3/a");
```

```csharp
Uri(Uri baseUri, string? relativeUri);
```

```csharp
static List<string> GetBookLinks(string url)
    {
        var bookLinks = new List<string>();
        HtmlDocument doc = GetDocument(url);
        HtmlNodeCollection linkNodes = doc.DocumentNode.SelectNodes("//h3/a");
        var baseUri = new Uri(url);
        foreach (var link in linkNodes)
        {
            string href = link.Attributes["href"].Value;
            bookLinks.Add(new Uri(baseUri, href).AbsoluteUri);
        }
        return bookLinks;
    }
```

```csharp
static void Main(string[] args)
{
    var bookLinks = GetBookLinks("http://books.toscrape.com/catalogue/category/books/mystery_3/index.html");
    Console.WriteLine("Found {0} links", bookLinks.Count);
}
```

```bash
dotnet run
```

```
Found 20 links
```

```csharp
public class Book
{
    public string Title { get; set; }
    public string Price { get; set; }
}
```

```xpath
//div[contains(@class,"product_main")]/p[@class="price_color"]
```

```csharp
static List<Book> GetBookDetails(List<string> urls)
{
    var books = new List<Book>();
    foreach (var url in urls)
    {
        HtmlDocument document = GetDocument(url);
        var titleXPath = "//h1";
        var priceXPath = "//div[contains(@class,\"product_main\")]/p[@class=\"price_color\"]";
        var book = new Book();
        book.Title = document.DocumentNode.SelectSingleNode(titleXPath).InnerText;
        book.Price = document.DocumentNode.SelectSingleNode(priceXPath).InnerText;
        books.Add(book);
    }
    return books;
}
```

```csharp
static void Main(string[] args)
{
    var bookLinks = GetBookLinks("http://books.toscrape.com/catalogue/category/books/mystery_3/index.html");
    Console.WriteLine("Found {0} links", bookLinks.Count);
    var books = GetBookDetails(bookLinks);
}
```

```csharp
static void exportToCSV(List<Book> books)
{
    using (var writer = new StreamWriter("./books.csv"))
    using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
    {
        csv.WriteRecords(books);
    }
}
```

```csharp
static void Main(string[] args)
{
    var bookLinks = GetBookLinks("http://books.toscrape.com/catalogue/category/books/mystery_3/index.html");
    var books = GetBookDetails(bookLinks);
    exportToCSV(books);
}
```

```bash
dotnet run
```


