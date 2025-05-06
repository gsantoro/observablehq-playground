---
sql:
  landscape: ./data/landscape.parquet
---

# CNCF landscape
This page shows my learnings about the CNCF landscape

## All projects with GH stars
There are many more projects that those listed here but I am only interested in the projects with GitHub stars.

Search for a project by start typing in the input box below.

Search is performed by all fields.

```sql id=all_projects_with_stars
select *
from landscape
where gh_stars > 0
order by gh_stars desc
```

```js
const search_all_projects_with_stars = view(
  Inputs.search(all_projects_with_stars, {
    placeholder: "Search projects all fields...",
  }));
```

```js 
Inputs.table(search_all_projects_with_stars.map(d => ({link: [d.name, d.homepage_url], ...d})), {
  columns: [
    "link",
    "gh_stars",
    "category", 
    "subcategory",
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
    link: d => htl.html`<a href="${d[1]}" target="_blank">${d[0]}</a>`,
  },
  select: false
})
```




## Search projects by their category
In this section we can search a project by the category/subcategory it belongs to.

```sql id=categories_with_gh_stars
select concat(category, ' > ', subcategory) as category, count(*) as items
from landscape
where gh_stars > 0
group by 1
order by 2 desc
```

```js
Plot.plot({
  marginLeft: 350,
  x: {
    grid: true,
    label: "Number of Items"
  },
  y: {
    label: null
  },
  marks: [
    Plot.barX(categories_with_gh_stars, {
      y: "category",
      x: "items",
      sort: {y: "-x"},
      fill: "steelblue",
    }),
    Plot.tip(categories_with_gh_stars, Plot.pointerX({x: "items", y: "category"}))
  ]
})
```

```js 
const chosenCategory = view(
  Inputs.text({
    label: "Category > Subcategory",
    placeholder: "Type to search subcategories...",
    value: "Platform > Certified Kubernetes - Distribution",
    datalist: Array.from(categories_with_gh_stars).map(d => d.category)
  }))
```

```sql id=projects_with_chosen_category
SELECT name, gh_stars, category || ' > ' || subcategory as category
  FROM landscape 
 WHERE category || ' > ' || subcategory = ${chosenCategory}
 and gh_stars > 0
 ORDER BY gh_stars desc
```

```js 
Inputs.table(Array.from(projects_with_chosen_category).map(d => ({link: [d.name, d.homepage_url], ...d})), {
  columns: [
    "link",
    "gh_stars",
    "category", 
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
    link: d => htl.html`<a href="${d[1]}" target="_blank">${d[0]}</a>`,
  },
  select: false
})