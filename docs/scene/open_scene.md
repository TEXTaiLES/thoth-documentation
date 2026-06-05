# Opening a scene with THOTH

To open a scene using the THOTH web app, open the following url on your web browser.

```
{base_url}/a/thoth/?s={scene_url}
```

where `base_url` is the base thoth uri and `scene_url` the uri of the scene. The default `base url` is [`http://localhost:8080`](http://localhost:8080). The `scene-uri` includes the user path. For  example, a scene id We use for testing is [`http://localhost:8080/a/thoth/?s=samples/venus`](http://localhost:8080/a/thoth/?s=samples/venus)

*For the version deployed on [Hestia](https://textailes.athenarc.gr/archive), we replace **?s=** with **?id=** for scene access.*

On success, this is what a loaded page should look like this:

<p align="center">
    <img src="../../assets/thoth_screenshot.png" alt="Scene Screenshot" width="800"/>
</p>

You can create a scene from the ATON front end (shu) or through a post request through the [ATON REST API](../api/rest.md).