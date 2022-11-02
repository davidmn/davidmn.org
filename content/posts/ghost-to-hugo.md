---
title: "Ghost to Hugo"
date: 2022-11-02T18:20:00Z
draft: false
---

As per my previous post, I destroyed my Ghost installation. Nginx was shafted, networking in general was shafted, node was shafted. It was a mess. So I gave up and decided to go with a nice static site. Just like I always should have.

I managed to fix the networking enough to SSH into it. Form their I dumped the mysql database into a file using a little bash

``` bash
mysqldump -u root -p ghost_production > ~/mysqldump
```

From there back to my local machine where I used rsync to get the files downloaded with the classic:

```bash
rsync -chavzPr --stats user@remote.host:/path/to/copy /path/to/local/storage
```

Then I had to mount the sql backup with a local instance of MySQL which I got up and running with brew (yes I use a Mac now). I had a poke around the schema with DBeaver. Which lead me to this horrible hacky C#:

```csharp
using MySqlConnector;
using ReverseMarkdown;

using var connection = new MySqlConnection("Server=localhost;port=3306;User ID=root;Password=YourPasswordHere;Database=ghost_production");
connection.Open();

using var command  = new MySqlCommand("SELECT title, slug, updated_at, html FROM posts", connection);
using var reader = command.ExecuteReader();

while (reader.Read())
{
    var title = reader.GetString(0);
    if(title == "(Untitled)")
    {
        continue;
    }
    var slug = reader.GetString(1);
    var updatedAt = reader.GetDateTime(2);
    var html = reader.GetString(3);
    var converter = new ReverseMarkdown.Converter();
    var markdown = converter.Convert(html);
    Console.WriteLine($"{title}  {slug} {updatedAt}");
    var lines = new List<string>();
    lines.Add("---");
    lines.Add($"title: \"{title}\"");
    lines.Add($"date: {updatedAt.ToString("o")}Z");
    lines.Add("draft: true");
    lines.Add("---");
    lines.Add("");
    lines.Add(markdown);
    File.WriteAllLines($"output/{slug}.md", lines);
}
```

The idea being I use the slug from Ghost as the file name which becomes the slug that Huge uses. Then faff around the other info to make the header of the TOML.

I had blank post lying around and rather than messing with the db I just skipped it.

The interesting bit was that Ghost stores each post's HTML in the database. That lets me use a lovely library called ReverseMarkdown to take that HTML and output the Markdown.

There's a little wrinkle in that some of the newer posts use the Figure element which ReverseMarkdown doesn't handle. There aren't many of them so I'll do some manual fixing.

Next up will be restoring all the posts that are pure text. Then I'll work out how to add images and repair the Figures.
