# Intro to XSLT Injection

## eXtensible Stylesheet Language Transformation (XSLT)

XSLT is a language that transforms XML documents by selecting specific nodes and changing the XML structure.

### Sample XML Document

```xml
<?xml version="1.0" encoding="UTF-8"?>
<fruits>
    <fruit>
        <name>Apple</name>
        <color>Red</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Banana</name>
        <color>Yellow</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Strawberry</name>
        <color>Red</color>
        <size>Small</size>
    </fruit>
</fruits>
```

### Common XSL Elements

- **`<xsl:template>`** - Defines an XSL template with a `match` attribute specifying the XML path
- **`<xsl:value-of>`** - Extracts the value of a node specified in the `select` attribute
- **`<xsl:for-each>`** - Loops over all nodes specified in the `select` attribute
- **`<xsl:sort>`** - Sorts loop elements by the `select` attribute with optional `order` parameter
- **`<xsl:if>`** - Tests conditions on a node using the `test` attribute

### Example: List All Fruits with Color

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/fruits">
        Here are all the fruits:
        <xsl:for-each select="fruit">
            <xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
        </xsl:for-each>
    </xsl:template>
</xsl:stylesheet>
```

**Output:**
```
Here are all the fruits:
    Apple (Red)
    Banana (Yellow)
    Strawberry (Red)
```

### Example: Filtered and Sorted List

```xslt
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="/fruits">
        Here are all fruits of medium size ordered by their color:
        <xsl:for-each select="fruit">
            <xsl:sort select="color" order="descending" />
            <xsl:if test="size = 'Medium'">
                <xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
            </xsl:if>
        </xsl:for-each>
    </xsl:template>
</xsl:stylesheet>
```

**Output:**
```
Here are all fruits of medium size ordered by their color:
    Banana (Yellow)
    Apple (Red)
```

## XSLT Injection

XSLT injection occurs when user input is inserted into XSL data before processing. Attackers can inject additional XSL elements that the XSLT processor executes during output generation.
