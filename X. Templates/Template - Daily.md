#{{date:YYYY}}/{{date:YYYY-MM}}/{{date:YYYY-MM-DD}}
# {{date:YYYY-MM-DD dddd}}
## Journal



---
## Á faire aujourd'hui !
```dataview

TASK WHERE !completed AND due <= date("{{date:YYYY-MM-DD}}") AND due != null

```


---
## ARHI ATS
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#arhi") 
AND !completed 
AND due != null
```


---
## ForHives 
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#forhives") 
AND !completed 
AND due != null
```

---
## Makeup 
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#makeup") 
AND !completed 
AND due != null
```

---
## ForMenu
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#formenu") 
AND !completed 
AND due != null
```

---
## Pro (Principal)
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#pro_principal") 
AND !completed 
AND due != null
```

---
## Obsidian Todos
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#obsidian-todo") 
AND !completed 
AND due != null
```

---
## Pro (Secondaire)
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#pro_secondaire") 
AND !completed 
AND due != null
```

---
## Cours
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#cours") 
AND !completed 
AND due != null
```

---
## Alternance
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE contains(file.tags,"#alternance") 
AND !completed 
AND due != null
```

---
## à faire Sur 2 mois 
```dataview 
TASK 
FROM "Y. Todo's & Listes" 
WHERE (
contains(file.tags,"#alternance") 
OR contains(file.tags,"#formenu") 
OR contains(file.tags,"#forhives") 
OR contains(file.tags,"#makeup") 
OR contains(file.tags,"#cours") 
OR contains(file.tags,"#pro_principal") 
OR contains(file.tags,"#pro_secondaire") 
OR contains(file.tags,"#obsidian-todo") 
)

AND !completed 
AND due - date(today) <= dur(60 days) 
AND due != null
```

---
## à faire (général) 
```dataview 
TASK WHERE !completed AND due - date(today) >= dur(0 days) 
```


---

---
---
---
## Created Today
```dataview

TABLE string(split(string(file.ctime), " - ")[0]) as "Created", file.mtime as "Last Modified"

WHERE contains(file.path, "{{date:YYYY-MM-DD}}")

  OR (file.cday = date("{{date:YYYY-MM-DD}}") AND !regexmatch("^\d{4}-\d{2}-\d{2}", file.name))

SORT file.name ASC

```
---
## Last Modified Today
```dataview

TABLE string(split(string(file.mtime), " - ")[0]) as "Last Modified"

WHERE !contains(file.path, "{{date:YYYY-MM-DD}}")

  AND !contains(file.path, "{{date:YYYY-MM-DD}} Daily Note")

  AND file.mday = date("{{date:YYYY-MM-DD}}")

SORT file.mtime DESC

```
---
## Other Tasks Completed Today
```dataview

TASK WHERE completion = date("{{date:YYYY-MM-DD}}")

  AND due != date("{{date:YYYY-MM-DD}}")

```
