---
title: "MYC Recital 圣诞节"
header:
  video:
    id: AMCY9D8b5IQ
    provider: youtube
categories:
  - Layout
  - Uncategorized
tags:
  - video
  - layout
---

This post should display a **header with a responsive video**, if the theme supports it.
Daniel's good work on MYC Chrismas Recital
## Settings

| Parameter  | Required     | Description |
|----------  |---------     | ----------- |
| `id`       | **Required** | ID of the video |
| `provider` | **Required** | Hosting provider of the video, either `youtube` or `vimeo` |

### YouTube

To embed the following YouTube video at url `https://www.youtube.com/watch?v=AMCY9D8b5IQ` (long version) or `https://youtu.be/AMCY9D8b5IQ` (short version) into a post or page's main content you'd use:

```liquid
{% raw %}{% include video id="AMCY9D8b5IQ" provider="youtube" %}{% endraw %}
```

{% include video id="XsxDH4HcOWA" provider="youtube" %}

To embed it as a video header you'd use the following YAML Front Matter
