---
date: <% tp.date.now("MM-DD-YYYY") %>
tags:
  - Daily
cssclasses:
  - daily
  - <%tp.date.now("dddd")%>
---
<%* await tp.file.rename(tp.date.now("MM-DD-YYYY") + " Daily Journal") %>
# DAILY NOTE
## <% tp.date.now("dddd, MMMM Do, YYYY") %>
***
### Journal
#### Time
Customize this template to your liking!
...
***
### Tasks
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

### References
- Reference 1
- Reference 2