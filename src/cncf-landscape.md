---
sql:
  landscape: ./data/cncf-landscape.parquet
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
    placeholder: "Search projects by all fields...",
  }));
```

```js 
Inputs.table(search_all_projects_with_stars.map(d => ({link: [d.name, d.repo_url], ...d})), {
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

Here is the list of all "Category > subcategory" grouped by the number of projects they have.

```sql id=categories_with_gh_stars display
select concat(category, ' > ', subcategory) as category, count(*) as projects
from landscape
where gh_stars > 0
group by 1
order by 2 desc
```

Filter all projects by their "Category > subcategory".

Type below the category/subcategory to start your search.

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
SELECT 
  name, 
  repo_url,
  gh_stars, 
  category || ' > ' || subcategory as category
  FROM landscape 
 WHERE category || ' > ' || subcategory = ${chosenCategory}
 and gh_stars > 0
 ORDER BY gh_stars desc
```

```js 
Inputs.table(Array.from(projects_with_chosen_category).map(d => ({link: [d.name, d.repo_url], ...d})), {
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