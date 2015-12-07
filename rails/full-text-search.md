# Full-Text Search in PostgreSQL 

Postgres offers full-text searching right out of the box. 
Context: We have a project with title "In hac habitasse platea dictumst"

```
Project.where("title Ilike :title", title: "Hac Habitasse platea")
=> 1 result.

Project.where("title Ilike :title", title: "Hac platea")
=> 0 result.

Project.where("title @@ :title", title: "Hac platea")
=> 1 result.
```