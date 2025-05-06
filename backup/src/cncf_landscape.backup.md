
## All projects
List of all projects

```sql id=all_projects
select * from landscape
```

```js
const search = view(
  Inputs.search(all_projects, {
    placeholder: "Search projects by all fields...",
  }));
```

```js 
Inputs.table(search, {
  columns: [
    "name", 
    "gh_stars",
    "category", 
    "subcategory",
    "homepage_url"
  ],
  header: {
    name: "Project name",
    gh_stars: "GitHub stars",
    category: "Category",
    subcategory: "Subcategory"
  },
  sort: "gh_stars",
  reverse: true,
  format: {
    homepage_url: d => htl.html`<a href="${d}" target="_blank">${d}</a>`
  },
  select: false
})
```



## All categories


```sql id=categories_by_item
select category, count(*) as items
from landscape
group by 1
order by 2 desc
```


```js
Plot.plot({
  marginLeft: 200,
  x: {
    grid: true,
    label: "Number of Items"
  },
  y: {
    label: null
  },
  marks: [
    Plot.barX(categories_by_item, {
      y: "category",
      x: "items",
      sort: {y: "-x"},
      fill: "steelblue"
    })
  ]
})
```



---


```js 
-- const chosenCategory = view(Inputs.select(categories_with_gh_stars, {
--   label: "Select a category", format: x => x.category
--   }))

-- const chosenCategory = view(Inputs.search(categories_with_gh_stars, {
--   label: "Search for a category", 
--   placeholder: "Type to search categories...",
--   format: x => x.category
-- }))

viewof chosenCategory = Inputs.text({
  label: "Subcategory",
  placeholder: "Type to subcategories...",
  datalist: categories_with_gh_stars.map(d => d.category)
})

-- SELECT name, gh_stars, category, subcategory
--   FROM landscape 
--  WHERE category || ' > ' || subcategory = ${chosenCategory.category}
--  and gh_stars > 0
--  ORDER BY gh_stars desc
```