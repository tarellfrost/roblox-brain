# UI reference library

Choose a visual style here, read its STYLE.md, and inspect its actual screenshot
files before translating the style into a game. Keep the Roblox boilerplate's
Studio authoring and UIController architecture.

| Style | Visual tags | Screens | Reference status |
| --- | --- | --- | --- |
| [Colorful Rounded](colorful-rounded/STYLE.md) | colorful, rounded, chunky, outlined, playful | HUD, shop, inventory | Template only; screenshots needed |

## Add screenshots to the first style

1. Add PNG/JPG screenshots under `colorful-rounded/screenshots/` using the names
   listed in STYLE.md. A full screen shows layout; a close crop shows details.
2. Record each image's source, creator, permission/reuse status, device, and the
   visual properties you want the agent to match in STYLE.md.
3. Replace the draft visual directions with observations from those images.
4. Mark the style ready in this index only when its listed screenshots exist and
   its source records are filled. A missing screen can remain explicitly absent.

## Agent workflow

- Match both the requested visual style and screen purpose. Read only the
  selected style and a small set of relevant screenshots.
- Open images with an available image-viewing tool. Searching captions or
  reading STYLE.md does not count as inspecting the images.
- Extract palette, typography, component shapes, outlines, spacing, hierarchy,
  and density. Keep one primary style per screen unless the user requests a mix.
- Reproduce the requested visual language in the target game's own content;
  record which referenced assets may actually be reused.
- Build authored UI in Studio and behavior in UIController screen modules.
  Compare a screenshot of the result at the target device sizes with references.
- Templates marked incomplete are written design briefs, not visual evidence.
  If files cannot be accessed, report that limitation before claiming a match.

## Access and installation

This is a root-level repository resource. A skills-only installation may omit
it. Keep the full repository available locally and give the agent its path, or
make the selected images available separately. The GUI skill can work without
this library; it must not claim it inspected inaccessible references.

This folder provides a searchable visual library. It does not include a vector
database, image embeddings, or an automated RAG service.

## Add another style later

Copy the first style folder, choose a distinct ID/title, update its description
and screenshot/source records, and add a row to this index. Avoid duplicating
the same images across folders; describe deliberate variations instead.
