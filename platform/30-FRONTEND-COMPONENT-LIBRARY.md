# Frontend Component Library

This document outlines the core user interface components for Chronicle's keyboard-driven frontend application, preventing random UI generation and maintaining design consistency.

---

## 1. Question Input
* **Purpose:** The primary portal for asking Chronicle questions.
* **Layout:** A prominent, clean input box placed centrally on the screen.
* **Features:**
  * **Multi-Line Support:** Auto-growing textarea that expands dynamically up to 8 lines.
  * **Slash Commands:** Typing `/` opens a command list to filter search (e.g., `/timeline`, `/github`, `/slack`, `/incidents`).
  * **Keyboard Shortcuts:** 
    * `Enter` to submit.
    * `Shift+Enter` to add a new line.
    * `Up` / `Down` arrows to navigate query history.
  * **History List:** Visual dropdown showing previous successful searches.

---

## 2. Answer Card
* **Purpose:** Renders the synthesized, cited response.
* **Layout:** Centered card with tight margins and clean typography.
* **Features:**
  * **Markdown Rendering:** Supports bolding, code syntax block coloring, and inline code formatting.
  * **Confidence Indicator:** Displays a small, colored pill (e.g., `Confidence: 94%`) with tooltips showing the underlying telemetry scoring.
  * **Inline Citations:** Synthesized text contains clickable anchors (e.g., `[Evidence-1]`). Hovering over a citation previews the source text.
  * **Timeline Link:** A quick-action button pointing directly to the timeline segment where the cited events occurred.

---

## 3. Evidence Panel
* **Purpose:** A side-by-side split screen showing the raw source material behind the citations.
* **Layout:** Slides in from the right edge when a citation is clicked.
* **Tab Categorization:**
  * **GitHub Tab:** Renders syntax-highlighted code blocks, commit diff annotations, or PR reviewer comments.
  * **Slack Tab:** Renders the Slack thread message chain, including user avatars, timestamps, and reaction emojis.
  * **Timeline Tab:** Shows the position of this evidence block within the local timeline.
  * **Documents Tab:** Displays the original documentation page or RFC file referenced.

---

## 4. Timeline Viewer
* **Purpose:** Navigates historical events on a temporal axis.
* **Layout:** Dense, vertical line with dot indicators representing events.
* **Features:**
  * **Zoom Control:** Slider or shortcuts (`+` / `-`) to zoom from hourly granularity out to monthly clusters.
  * **System Filters:** Checkboxes to hide or show specific event types (e.g., hiding Slack clutter to focus only on git deploys).
  * **Search & Grouping:** Grouping multiple sequential commits into a single node.

---

## 5. Graph Viewer
* **Purpose:** Visualizes causal relationships between code symbols and conversations.
* **Layout:** Ephemeral, interactive force-directed canvas.
* **Features:**
  * **Node-Edge Highlighting:** Hovering over a node highlights its direct causal path (e.g. `User ──► Commit ──► Incident`).
  * **Graph Navigation:** Drag-to-pan and scroll-to-zoom controls.

---

## 6. Command Palette
* **Purpose:** Global, keyboard-driven navigation launcher.
* **Trigger:** `Cmd+K` (macOS) or `Ctrl+K` (Windows/Linux).
* **Features:** Allows users to search docs, switch workspaces, configure integrations, invite teammates, and change themes using only the keyboard.

---

## 7. Workspace Switcher
* **Purpose:** Allows users to hop between organization environments (e.g., switching from personal scratch space to the company workspace).
* **Layout:** Keyboard-navigable sidebar switcher.

---

## 8. Integration Dashboard
* **Purpose:** Displays integration states.
* **Layout:** Grid cards showing connection states for GitHub, Slack, Linear, Notion, and Jira.
* **Features:** Shows green/gray indicators, sync status progress bars, and token validation metrics.
