<%*
// Ρώτα για τίτλο
const title = await tp.system.prompt("Τίτλος post:");
if (!title) return;

// Δημιούργησε slug (ελληνικά → λατινικά)
const greekMap = {
  'α':'a','β':'v','γ':'g','δ':'d','ε':'e','ζ':'z','η':'i','θ':'th',
  'ι':'i','κ':'k','λ':'l','μ':'m','ν':'n','ξ':'x','ο':'o','π':'p',
  'ρ':'r','σ':'s','ς':'s','τ':'t','υ':'y','φ':'f','χ':'ch','ψ':'ps','ω':'o',
  'ά':'a','έ':'e','ή':'i','ί':'i','ό':'o','ύ':'y','ώ':'o','ϊ':'i','ϋ':'y',
  'ΐ':'i','ΰ':'y'
};
const slug = title
  .toLowerCase()
  .split('')
  .map(c => greekMap[c] || c)
  .join('')
  .replace(/[^a-z0-9\s-]/g, '')    // μόνο λατινικά, αριθμοί
  .replace(/\s+/g, '-')            // spaces → dashes
  .replace(/-+/g, '-')             // πολλαπλά dashes → ένα
  .replace(/^-|-$/g, '')           // trim dashes
  .trim();

// Διάβασε categories από υπάρχοντα posts
const categories = new Set();
const posts = app.vault.getMarkdownFiles().filter(f => f.path.startsWith("content/posts/"));
for (const post of posts) {
  const cache = app.metadataCache.getFileCache(post);
  if (cache?.frontmatter?.categories) {
    const cats = cache.frontmatter.categories;
    (Array.isArray(cats) ? cats : [cats]).forEach(c => categories.add(c));
  }
}
const catList = [...categories].sort();
catList.push("➕ Νέα κατηγορία...");

// Ρώτα για category
let category = await tp.system.suggester(catList, catList, false, "Επίλεξε κατηγορία:");
if (category === "➕ Νέα κατηγορία...") {
  category = await tp.system.prompt("Όνομα νέας κατηγορίας:");
}
if (!category) return;

// Διάβασε tags από υπάρχοντα posts
const tagsSet = new Set();
for (const post of posts) {
  const cache = app.metadataCache.getFileCache(post);
  if (cache?.frontmatter?.tags) {
    const t = cache.frontmatter.tags;
    (Array.isArray(t) ? t : [t]).forEach(tag => tagsSet.add(tag));
  }
}
const tagList = [...tagsSet].sort();
tagList.push("✅ Τέλος επιλογής");
tagList.push("➕ Νέο tag...");

// Ρώτα για tags (multi-select loop)
const selectedTags = [];
let selectingTags = true;
while (selectingTags) {
  const available = tagList.filter(t => !selectedTags.includes(t));
  const picked = await tp.system.suggester(available, available, false, `Tags (επιλεγμένα: ${selectedTags.length}):`);
  if (!picked || picked === "✅ Τέλος επιλογής") {
    selectingTags = false;
  } else if (picked === "➕ Νέο tag...") {
    const newTag = await tp.system.prompt("Όνομα νέου tag:");
    if (newTag) selectedTags.push(newTag.trim());
  } else {
    selectedTags.push(picked);
  }
}
const tags = selectedTags.map(t => `"${t}"`).join(', ');

// Ρώτα για περιγραφή
const description = await tp.system.prompt("Σύντομη περιγραφή:", "");

// Σημερινή ημερομηνία
const date = tp.date.now("YYYY-MM-DD");

// Δημιούργησε τον φάκελο και μετακίνησε το αρχείο
const folderPath = `content/posts/${slug}`;
await app.vault.createFolder(folderPath);
await tp.file.move(`${folderPath}/index`);
await tp.file.rename("index");
-%>
---
title: "<% title %>"
date: <% date %>
draft: true
categories: ["<% category %>"]
tags: [<% tags %>]
description: "<% description %>"
image: "cover.jpg"
---


**Workflow 👇🏼**
1. Cover photo → αντέγραψε/μετονόμασε σε cover.jpg στον φάκελο του post (1200x630, <300KB)
2. In-content photos → paste/drag μέσα στο Obsidian
3. Markdown → `![alt text](photo.jpg)`










  