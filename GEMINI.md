# Conductor-Jules Context

If a user mentions a "plan" or asks about the plan, and they have used the conductor-jules extension in the current session, they are likely referring to the `conductor-jules/tracks.md` file or one of the track plans (`conductor-jules/tracks/<track_id>/plan.md`).

## Universal File Resolution Protocol

**PROTOCOL: How to locate files.**
To find a file (e.g., "**Product Definition**") within a specific context (Project Root or a specific Track):

1.  **Identify Index:** Determine the relevant index file:
    -   **Project Context:** `conductor-jules/index.md`
    -   **Track Context:**
        a. Resolve and read the **Tracks Registry** (via Project Context).
        b. Find the entry for the specific `<track_id>`.
        c. Follow the link provided in the registry to locate the track's folder. The index file is `<track_folder>/index.md`.
        d. **Fallback:** If the track is not yet registered (e.g., during creation) or the link is broken:
            1. Resolve the **Tracks Directory** (via Project Context).
            2. The index file is `<Tracks Directory>/<track_id>/index.md`.

2.  **Check Index:** Read the index file and look for a link with a matching or semantically similar label.

3.  **Resolve Path:** If a link is found, resolve its path **relative to the directory containing the `index.md` file**.
    -   *Example:* If `conductor-jules/index.md` links to `./workflow.md`, the full path is `conductor-jules/workflow.md`.

4.  **Fallback:** If the index file is missing or the link is absent, use the **Default Path** keys below.

5.  **Verify:** You MUST verify the resolved file actually exists on the disk.

**Standard Default Paths (Project):**
- **Product Definition**: `conductor-jules/product.md`
- **Tech Stack**: `conductor-jules/tech-stack.md`
- **Workflow**: `conductor-jules/workflow.md`
- **Product Guidelines**: `conductor-jules/product-guidelines.md`
- **Tracks Registry**: `conductor-jules/tracks.md`
- **Tracks Directory**: `conductor-jules/tracks/`

**Standard Default Paths (Track):**
- **Specification**: `conductor-jules/tracks/<track_id>/spec.md`
- **Implementation Plan**: `conductor-jules/tracks/<track_id>/plan.md`
- **Metadata**: `conductor-jules/tracks/<track_id>/metadata.json`

