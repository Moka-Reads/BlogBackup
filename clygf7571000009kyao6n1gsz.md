---
title: "Why Zed is now my Main Editor"
datePublished: Wed Jul 10 2024 22:37:11 GMT+0000 (Coordinated Universal Time)
cuid: clygf7571000009kyao6n1gsz
slug: why-zed-is-now-my-main-editor
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4Mw7nkQDByk/upload/658305a28d0e3168b7694c8ea10204d1.jpeg
tags: opensource, rust, zed

---

Zed has been working on Linux support for six months, and I have been patiently waiting; in the last couple of weeks, I manually installed it from their GitHub release and gave it a spin.

Here's the thing: usually, when someone says they have this new fast thing, it is somewhat or not too noticeable, but from someone who mainly uses VSCode for most languages like C, Zig, websites, and Rust Rover for Rust, I couldn't believe the difference I felt going into Zed.

When I manually installed it and checked how much RAM the editor was using, it was close to 500MB. I have now installed Zed 0.143.6, which is the version that came out with Linux support completed. I am now using as much as 150 MB of RAM!!! This is more relevant to my Rust development since Rust Rover would use close to 8GB of RAM and still have some stutters.

Now, the next thing is smoothness; with the editor having GPU acceleration with Vulkan, I can feel that smoothness with the inlay hints, whereas Rust and Zig on VSCode were unreliable. While Rust Rover was consistent, the stutters didn't make it a smooth experience.

The Copilot AI is straight out of the box, and it's nice to be built in. They have the appropriate toggles for disabling it.

Another thing I loved is the theme previews. You toggle between the themes and can select one, which will show you how it looks in real-time. It's such a nice experience.

Look, I can list all the quirks I love about the editor, but to summarize, this is an amazing editor if you're looking for something super smooth, fast, low-consuming, and with all the modern features found in editors.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720650686247/9cbe34e9-c646-4795-9372-46e7717fda3a.png align="center")

Currently, my Zed configuration is also the following:

```json
// Zed settings
//
// For information on how to configure Zed, see the Zed
// documentation: https://zed.dev/docs/configuring-zed
//
// To see all of Zed's default settings without changing your
// custom settings, run the `open default settings` command
// from the command palette or from `Zed` application menu.
{
  "assistant": {
    "provider": {
      "name": "anthropic",
      "default_model": "claude-3-5-sonnet-20240620"
    },
    "version": "1"
  },
  "theme": "Ayu Dark",
  "inline_completions": {
    "disabled_globs": [".env"]
  },
  "show_inline_completions": true,
  "languages": {
    "JSON": {
      "show_inline_completions": false
    },
    "C": {
      "show_inline_completions": true,
      "format_on_save": "on",
      "formatter": "prettier"
    },
    "Rust": {
      "show_inline_completions": true
    },
    "Zig": {
      "show_inline_completions": true,
      "format_on_save": "on",
      "formatter": "language_server"
    }
  },
  "autosave": "on_focus_change",
  "multi_cursor_modifier": "alt",
  "current_line_highlight": "line",
  "ui_font_size": 16,
  "buffer_font_size": 16,
  "git": {
    "git_gutter": "tracked_files"
  },
  "terminal": {
    "alternate_scroll": "off",
    "blinking": "terminal_controlled",
    "copy_on_select": true,
    "option_as_meta": false,
    "button": true,
    "toolbar": {
      "title": true
    }
  }
}
```