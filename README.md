rome
====

ROME the RSS and ATOM parser


This tracks https://rometools.jira.com/svn/ROME/trunk/ currently.

example usage:
==============

```
require 'java'
require 'getopts'

include_class 'java.lang.System'
include_class 'java.net.URL'
include_class 'java.util.ArrayList'
include_class('java.util.Date') {|p,n| "J" + n }
include_class 'javax.xml.transform.TransformerFactory'
include_class 'javax.xml.transform.dom.DOMSource'
include_class 'javax.xml.transform.stream.StreamSource'
include_class 'javax.xml.transform.stream.StreamResult'
include_class 'com.sun.syndication.feed.synd.SyndFeedImpl'
include_class 'com.sun.syndication.io.SyndFeedInput'
include_class 'com.sun.syndication.io.SyndFeedOutput'
include_class 'com.sun.syndication.io.XmlReader'

getopts('p:')

hours = 1000 * 60 * 60 * ($OPT['p'] ? $OPT['p'].to_i : 24)
now = JDate.new
earlier = JDate.new(now.getTime - hours)

aFeed = SyndFeedImpl.new
aFeed.setFeedType "rss_2.0"
aFeed.setTitle "Aggregated RSS Feed"
aFeed.setDescription "Anonymous Aggregated Feed"
aFeed.setAuthor "anonymous"
aFeed.setLink "http://www.anonymous.com"

aEntries = ArrayList.new

ARGV.each do |arg| 
  feed = SyndFeedInput.new.build(XmlReader.new(URL.new(arg)))

  feed.entries.each do |entry|
    entry.setTitle "#{feed.getTitle}: #{entry.getTitle}"

    aEntries << entry
  end
end

aEntries = aEntries.select { |e| e.getPublishedDate.between?(earlier, now) }
aEntries.sort! { |a, b| b.getPublishedDate <=> a.getPublishedDate }
aFeed.setEntries aEntries

document = SyndFeedOutput.new.outputW3CDom(aFeed)
stylesheet = StreamSource.new("rss.xslt")
result = StreamResult.new(System.out)
transformer = TransformerFactory.newInstance.newTransformer(stylesheet)
transformer.transform(DOMSource.new(document), result)
```
(source: http://web.archive.org/web/20081211094858/http://www.tc.umn.edu/~enebo/snippets/rss.rb from http://www.tbray.org/ongoing/When/200x/2005/08/31/Ruby-Rome )
