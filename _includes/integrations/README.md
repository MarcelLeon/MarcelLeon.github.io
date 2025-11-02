# Third-Party Integrations

This directory contains modular third-party service integrations for the blog.

## Structure

- `analytics.html` - Analytics services (Google Analytics, Baidu Tongji)
- `comments.html` - Comment systems (Disqus, Netease)
- `custom-scripts.html` - Custom integrations (Dify, chatbots, etc.)

## Usage

### Analytics Integration

Configure in `_config.yml`:

```yaml
# Google Analytics
ga_track_id: 'UA-xxxxxx-xx'
ga_domain: yourdomain.com

# Baidu Analytics (optional)
ba_track_id: 'your-baidu-track-id'
```

### Comments Integration

Configure in `_config.yml`:

```yaml
# Disqus
disqus_username: your-disqus-shortname

# Netease Comments (optional)
netease_comment: true
```

### Custom Scripts Integration (Dify, etc.)

Configure in `_config.yml`:

```yaml
integrations:
  # Dify AI Assistant
  dify:
    enabled: true
    type: iframe  # or 'script'
    url: "https://your-dify-instance.com/embed"
    style: "width: 100%; height: 600px; border: none;"
    id: "dify-widget"
    class: "dify-iframe"
    # For script type:
    # init_script: |
    #   window.difyChatbotConfig = { ... };

  # Custom Scripts
  custom_scripts:
    - name: "My Custom Script"
      enabled: true
      type: external  # or 'inline'
      url: "https://example.com/script.js"
      position: all  # 'all', 'post', 'page', etc.
      async: true
      defer: false
    - name: "Analytics Helper"
      enabled: true
      type: inline
      position: post
      content: |
        console.log('Post page loaded');
```

## Adding New Integrations

1. Create a new HTML file in this directory (e.g., `my-service.html`)
2. Include it in the appropriate layout file (e.g., `_layouts/default.html`)
3. Add configuration options in `_config.yml`
4. Document the integration in this README

## Implementation

Include these integrations in your layouts:

```liquid
<!-- In _layouts/post.html or _layouts/default.html -->
{% include integrations/analytics.html %}
{% include integrations/comments.html %}
{% include integrations/custom-scripts.html %}
```
