---
title: Moving to React Suspense
date: "2019-05-31"
---

Loading stuff is hard. And you probably catch yourself constantly writing code that decides to show or hide content to the user, or show a loading spinner while the data is getting there. It's a good practise, especially if your user is on slow connection - in that case you really want to inform your customer that something is happening and so you end up making your ap responsive to data fetching.

## Use lazy loading - until 10:58

:)

## Use Suspense for assets - 11:15

Video, audio, script tags, style sheets, fonts. You can pause rendering or difficult loading states.

```jsx
import { unstable_createResource } from "react-cache"

const ImageResource = unstable_createResource(
  source =>
    new Promise(resolve => {
      const img = new Image()
      img.src = source
      img.onload = resolve
    })
)

const Img = ({ src, alt, ...props }) => {
  ImageResource.read(src)
  return <img src={src} alt={alt} {...props} />
}

function ArtistHeader({ artist }) {
  return (
    <div>
      <React.Suspense
        maxDuration={500}
        fallback={
          <img
            className="artist-image preview"
            src={artist.images[2].url}
            alt={artist.name}
          />
        }
      >
        <Img
          className="artist-image loaded"
          src={artist.images[0].url}
          alt={artist.name}
        />
      </React.Suspense>
    </div>
  )
}
```
