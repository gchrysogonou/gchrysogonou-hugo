<%*
// Ρώτα για τίτλο
const title = await tp.system.prompt("Τίτλος post:");
if (!title) return;

// Δημιούργησε slug (για φάκελο)
const slug = title
  .toLowerCase()
  .normalize('NFD')
  .replace(/[\u0300-\u036f]/g, '') // αφαίρεση τόνων
  .replace(/[^a-z0-9\s-]/g, '')    // μόνο γράμματα, αριθμοί
  .replace(/\s+/g, '-')            // spaces → dashes
  .replace(/-+/g, '-')             // πολλαπλά dashes → ένα
  .trim();

// Ρώτα για tags
const tagsInput = await tp.system.prompt("Tags (χωρισμένα με κόμμα):", "");
const tags = tagsInput ? tagsInput.split(',').map(t => `"${t.trim()}"`).join(', ') : "";

// Ρώτα για περιγραφή
const description = await tp.system.prompt("Σύντομη περιγραφή:", "");

// Σημερινή ημερομηνία
const date = tp.date.now("YYYY-MM-DD");

// Δημιούργησε τον φάκελο και μετακίνησε το αρχείο
const folderPath = `content/posts/${slug}`;
await app.vault.createFolder(folderPath);
await tp.file.move(`${folderPath}/index`);

// Rename το αρχείο σε index.md
await tp.file.rename("index");
-%>
---
title: "<% title %>"
date: <% date %>
draft: true
tags: [<% tags %>]
description: "<% description %>"
image: "cover.jpg"
---

<% tp.file.cursor() %>
