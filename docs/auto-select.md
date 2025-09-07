# Auto-Select CSS Selector Guide

This guide shows how to craft precise CSS selectors for the active tab, with practical examples taken from the built-in `urlPageActionRules.toml`.

## Quick Start
1. Open Firefox DevTools (F12) on the active tab.
2. Use **Element Picker** (Ctrl+Shift+C) to inspect the target element.
3. Copy the initial selector: right-click → **Copy** → **Copy CSS Selector**.
4. Refine the selector using the patterns below, then test in the DevTools **Console** with
   `document.querySelectorAll("YOUR_SELECTOR")`.

## Essential CSS Selector Patterns

### Basic Selectors
| Pattern | Description | Example from Rules |
|---------|-------------|-------------------|
| `#id` | Element with ID | `article#story` *(nytimes.com)* |
| `.class` | Element with class | `.article-content` *(github.com)* |
| `element` | Element type | `article`, `h1` |
| `[attr="value"]` | Attribute selector | `a[target="_blank"]` |

### Advanced Attribute Selectors
| Pattern | Description | Example from Rules |
|---------|-------------|-------------------|
| `[class^="prefix"]` | Class starts with | `[class^="gridLayout_topContent_"]` *(bloomberg.com)* |
| `[class*="substring"]` | Class contains | `[class*="article-body__trust-badge"]` *(reuters.com)* |
| `[class$="suffix"]` | Class ends with | `[class$="-bottom-bar"]` *(finance.sina.com.cn)* |
| `[class~="word"]` | Class contains exact word | `[class~="article"]` |

### Relationship Selectors
| Pattern | Description | Example from Rules |
|---------|-------------|-------------------|
| `ancestor descendant` | Any descendant | `article header` *(nytimes.com)* |
| `parent > child` | Direct child | `.repository-content > .Box-body` *(github.com)* |
| `prev + next` | Immediate sibling (right after) | `.detail-option-box + div` *(cls.cn)* |
| `prev ~ siblings` | All following siblings (all after) | `.detail-content-tags ~ div` *(stcn.com)* |
| `:has(child)` | Parent containing child | `.f-l:has(.detail-content)` *(cls.cn)* |

### Pseudo-class Selectors
| Pattern | Description | Example from Rules |
|---------|-------------|-------------------|
| `:not(selector)` | Exclude elements | `div:not(.ad)` |
| `:first-child` | First child | `article:first-child` |
| `:last-child` | Last child | `li:last-child` |
| `:nth-child(n)` | Specific child | `li:nth-child(3)` |

## Practical Examples from urlPageActionRules.toml

### 1. Complex Class Patterns
Bloomberg uses dynamic class names with prefixes that change with site updates. The rules target stable prefixes:
```
[class^="gridLayout_topContent_"]      # Selects elements where class starts with "gridLayout_topContent_"
[class^="gridLayout_featureTopContent_"] # Selects elements where class starts with "gridLayout_featureTopContent_"
[class^="BasicByline_byline__"]         # Selects elements where class starts with "BasicByline_byline__"
```

### 2. Selecting All Siblings After an Element
The `~` (general sibling combinator) selects all matching elements that come after a specified element:
```css
.detail-option-box ~ div                # Selects all div elements that come after .detail-option-box (cls.cn)
.detail-content-tags ~ div              # Selects all div elements that come after .detail-content-tags (stcn.com)
```

### 3. Selecting the Immediate Next Sibling
The `+` (adjacent sibling combinator) selects the very next sibling element:
```css
.detail-option-box + div                # Selects the div immediately following .detail-option-box (cls.cn)
#bannedNotice + div                     # Selects the div immediately following #bannedNotice (sohu.com)
```

### 4. Selecting Parents with Specific Children
The `:has()` pseudo-class selects elements that contain specific children:
```css
.f-l:has(.detail-content)               # Selects .f-l elements that contain .detail-content children (cls.cn)
.p_x_20:has(h1)                         # Selects .p_x_20 elements that contain h1 headings (cb.com.cn)
div:has(h2)                             # targets any <div> element that contains an <h2> as a descendant (direct or indirect child)
div:has(> h2)                           # Targets divs that have h2 as a direct child
```

### 5. Hiding Unwanted Elements
Use precise selectors to hide ads, comments, and other unwanted content:
```css
#popover-wrapper                        # Hides the popover wrapper (bbc.com)
.statement                              # Hides statements (sohu.com, yicai.com)
.comment-view                           # Hides comment sections (jiemian.com)
[class$="-bottom-bar"]                  # Hides elements with classes ending in "-bottom-bar" (finance.sina.com.cn)
```

### 6. Combining Multiple Selectors
Separate selectors with commas to target multiple elements:
```css
h1, #top_bar_wrap, #artibody            # Selects multiple important content areas (finance.sina.com.cn)
.entry-head, .entry-content             # Selects both header and content sections (gamelook.com.cn)
```

### 7. Wildcard Domain Matching
Use `*.subdomain` to match all subdomains:
```
["*.substack.com"]                      # Matches any substack subdomain (e.g., blog.substack.com, news.substack.com)
```

### 8. Attribute Selectors for Dynamic Content
Target elements with specific attribute patterns:
```css
a[href^="https://acttg.eastmoney.com"]  # Selects links with href starting with specific URL (eastmoney.com)
a[href^="https://www.jiemian.com/about/copyright"] # Selects copyright links (jiemian.com)
```

## Testing Your Selectors
Always test your selectors in the browser console:
```javascript
// Test selectors
document.querySelectorAll('article, h1')

// Count matches
document.querySelectorAll('[class^="gridLayout_topContent_"]').length

// Preview what will be selected/hidden
Array.from(document.querySelectorAll('.detail-option-box ~ div')).forEach(el => {
    el.style.border = '2px solid red'
})
```

## Best Practices
1. **Be specific**: Avoid overly broad selectors that may match unintended elements
2. **Test across pages**: Ensure selectors work consistently across different articles on the same site
3. **Use DevTools**: Inspect element structure to find the most reliable selectors
4. **Combine select/hide**: Use both select and hide arrays to fine-tune content extraction
5. **Order matters**: More specific selectors should come first to ensure proper matching
