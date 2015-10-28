# 4 Expert Tips for Getting the Most Out of Google Fonts


## Tip #1: Request Multiple Fonts in a Single Request

Using different fonts in heading and paragraph text can often improve readability. However, we also know that requesting many small files from a server is significantly slower that requesting one large file.

So, instead of requesting each font separately, it's advantageous to combine those multiple font requests into one. As an example, let's say you wish to load two fonts — Lato and Raleway. The proper way to load the fonts would be:

```ruby
  <link href='https://fonts.googleapis.com/css?family=Lato|Raleway' rel='stylesheet' type='text/css'>
```

Though this no difference to the total number of bits downloaded, the number of HTTP requests is reduced, and this has been proven to improve page load times. The more fonts you are using, the bigger the potential saving – though there are good other good reasons for limiting the number of external fonts you use.

Additionally, this method makes the code is more concise and readable. You only need to look at one line to understand all the fonts that your webpage is requesting.

## Tip #2: Optimize to Only Send the Characters You Need 

In specific situations, you might need to use only a strictly limited set of characters from a given font. This may also be the case when you only require the digits from a given font.

In cases like this, it's simple to reduce the request size by cherry-picking the specific characters we need by using the text= value in your font URL. This way Google will return the version of the font file you requested optimized for your specific needs. This can dramatically reduce the file-size of the font that is supplied – particularly with intricate display fonts and elaborate calligraphic typefaces.

Consider the following example:
```ruby
<link href='https://fonts.googleapis.com/css?family=Raleway&text=SitePoint' rel='stylesheet' type='text/css'>
```

## Tip #3: Specify Font Styles and Script Subsets

There are times when you may want the italic or bold version of a font. In situations like these you can specify the font style using i and b respectively after the font name separated by a :.

Here is an example:

```ruby
<link href='https://fonts.googleapis.com/css?family=Lato:i|Raleway:b' rel='stylesheet' type='text/css'>
```

You can also use bi to load a bold italic font style if available.

If you want a different variation of a bold font you can use a numerical weight. Raleway, for example, has four versions of bold with weight 600,700,800 and 900. To request the ultra bold font you need to call:

```ruby
<link href='https://fonts.googleapis.com/css?family=Raleway:900' rel='stylesheet' type='text/css'>
```

If you are not comfortable with the abbreviations, you are free to specify the full name for a font style, as I've done in the next example:

```ruby
<link href='https://fonts.googleapis.com/css?family=Lato:300italic' rel='stylesheet' type='text/css'>
```

In this case, instead of loading a normal italic font, we are loading the light version.

Some fonts in the directory support multiple scripts like Latin and Greek. One example of such a font is Roboto Mono. To request the Greek subset of the font you can use the following URL:

```ruby
<link href='https://fonts.googleapis.com/css?family=Roboto+Mono&subset=greek' rel='stylesheet' type='text/css'>
```


