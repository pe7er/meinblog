---
date: 2021-05-07
title: 'Unterschiedliche Karten mit Mapbox GL ermöglichen'
template: post
thumbnail: '../../thumbnails/maplibre.png'
slug: maplibregl-style-switcher
langKey: de
categories:
  - MaplibreGL
tags:
  - MaplibreGL
  - geografische Daten
  - Geoinformationssystem
  - JavaScript-Bibliothek
  - Vektor-Tiles
  - Karten
  - Style-Switcher
---

Jeder hat seine Vorliebe. Mancher bevorzugt Satellitenaufnahmen. Ein anderer gemalte Karten. Zusätzlich bieten unterschiedliche Style je nach Anwendungszweck Vor- und Nachteile. Deshalb ist ideal, wenn es man es Websitebesuchern ermöglicht, die Lieblingskarte per Steuerelement auszuwählen. Hier ein Beispiel für einen Kartentypwechsler oder Style-Switcher:

[![Full Screen Map with Style Switcher Gatsby Mapbox GL Starter](https://user-images.githubusercontent.com/9974686/97810149-18a13680-1c72-11eb-9efa-7fbfe67efd11.png)](https://astridx.github.io/gatsbystarter/gatsby-starter-mapbox-examples/map-style-switcher)

```html {numberLines: -2}
<!--  https://raw.githubusercontent.com/astridx/maplibreexamples/main/plugins/maplibre-style-switcher.html -->

<html>
  <head>
    <title>Mapbox style switcher</title>
    <meta charset="UTF-8" />
    <script src="../.env"></script>
    <script src="https://unpkg.com/maplibre-gl@1.14.0-rc.1/dist/maplibre-gl.js"></script>
    <link
      href="https://unpkg.com/maplibre-gl@1.14.0-rc.1/dist/maplibre-gl.css"
      rel="stylesheet"
    />
    <script>
      class MapboxStyleSwitcherControl {
        constructor(styles, defaultStyle) {
          this.styles = styles || MapboxStyleSwitcherControl.DEFAULT_STYLES
          this.defaultStyle =
            defaultStyle || MapboxStyleSwitcherControl.DEFAULT_STYLE
          this.onDocumentClick = this.onDocumentClick.bind(this)
        }
        getDefaultPosition() {
          const defaultPosition = 'top-right'
          return defaultPosition
        }
        onAdd(map) {
          this.map = map
          this.controlContainer = document.createElement('div')
          this.controlContainer.classList.add('mapboxgl-ctrl')
          this.controlContainer.classList.add('mapboxgl-ctrl-group')
          this.mapStyleContainer = document.createElement('div')
          this.styleButton = document.createElement('button')
          this.styleButton.type = 'button'
          this.mapStyleContainer.classList.add('mapboxgl-style-list')
          for (const style of this.styles) {
            const styleElement = document.createElement('button')
            styleElement.type = 'button'
            styleElement.innerText = style.title
            styleElement.classList.add(style.title.replace(/[^a-z0-9-]/gi, '_'))
            styleElement.dataset.uri = JSON.stringify(style.uri)
            styleElement.addEventListener('click', (event) => {
              const srcElement = event.srcElement
              if (srcElement.classList.contains('active')) {
                return
              }
              this.map.setStyle(JSON.parse(srcElement.dataset.uri))
              this.mapStyleContainer.style.display = 'none'
              this.styleButton.style.display = 'block'
              const elms = this.mapStyleContainer.getElementsByClassName(
                'active'
              )
              while (elms[0]) {
                elms[0].classList.remove('active')
              }
              srcElement.classList.add('active')
            })
            if (style.title === this.defaultStyle) {
              styleElement.classList.add('active')
            }
            this.mapStyleContainer.appendChild(styleElement)
          }
          this.styleButton.classList.add('mapboxgl-ctrl-icon')
          this.styleButton.classList.add('mapboxgl-style-switcher')
          this.styleButton.addEventListener('click', () => {
            this.styleButton.style.display = 'none'
            this.mapStyleContainer.style.display = 'block'
          })
          document.addEventListener('click', this.onDocumentClick)
          this.controlContainer.appendChild(this.styleButton)
          this.controlContainer.appendChild(this.mapStyleContainer)
          return this.controlContainer
        }
        onRemove() {
          if (
            !this.controlContainer ||
            !this.controlContainer.parentNode ||
            !this.map ||
            !this.styleButton
          ) {
            return
          }
          this.styleButton.removeEventListener('click', this.onDocumentClick)
          this.controlContainer.parentNode.removeChild(this.controlContainer)
          document.removeEventListener('click', this.onDocumentClick)
          this.map = undefined
        }
        onDocumentClick(event) {
          if (
            this.controlContainer &&
            !this.controlContainer.contains(event.target) &&
            this.mapStyleContainer &&
            this.styleButton
          ) {
            this.mapStyleContainer.style.display = 'none'
            this.styleButton.style.display = 'block'
          }
        }
      }
      // https://cloud.maptiler.com/maps/
      MapboxStyleSwitcherControl.DEFAULT_STYLE = 'Streets'
      MapboxStyleSwitcherControl.DEFAULT_STYLES = [
        {
          title: 'Baisc',
          uri:
            'https://api.maptiler.com/maps/basic/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Light',
          uri:
            'https://api.maptiler.com/maps/bright/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Outdoors',
          uri:
            'https://api.maptiler.com/maps/outdoor/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Satellite Hybrid',
          uri:
            'https://api.maptiler.com/maps/hybrid/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Streets',
          uri:
            'https://api.maptiler.com/maps/streets/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
      ]
    </script>

    <style>
      .mapboxgl-style-list {
        display: none;
      }

      .mapboxgl-ctrl-group .mapboxgl-style-list button {
        background: none;
        border: none;
        cursor: pointer;
        display: block;
        font-size: 14px;
        padding: 8px 8px 6px;
        text-align: right;
        width: 100%;
        height: auto;
      }

      .mapboxgl-style-list button.active {
        font-weight: bold;
      }

      .mapboxgl-style-list button:hover {
        background-color: rgba(0, 0, 0, 0.05);
      }

      .mapboxgl-style-list button + button {
        border-top: 1px solid #ddd;
      }

      .mapboxgl-style-switcher {
        background-image: url(data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iaXNvLTg4NTktMSI/PjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+PHN2ZyB2ZXJzaW9uPSIxLjEiIGlkPSJDYXBhXzEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHg9IjBweCIgeT0iMHB4IiB3aWR0aD0iNTQuODQ5cHgiIGhlaWdodD0iNTQuODQ5cHgiIHZpZXdCb3g9IjAgMCA1NC44NDkgNTQuODQ5IiBzdHlsZT0iZW5hYmxlLWJhY2tncm91bmQ6bmV3IDAgMCA1NC44NDkgNTQuODQ5OyIgeG1sOnNwYWNlPSJwcmVzZXJ2ZSI+PGc+PGc+PGc+PHBhdGggZD0iTTU0LjQ5NywzOS42MTRsLTEwLjM2My00LjQ5bC0xNC45MTcsNS45NjhjLTAuNTM3LDAuMjE0LTEuMTY1LDAuMzE5LTEuNzkzLDAuMzE5Yy0wLjYyNywwLTEuMjU0LTAuMTA0LTEuNzktMC4zMThsLTE0LjkyMS01Ljk2OEwwLjM1MSwzOS42MTRjLTAuNDcyLDAuMjAzLTAuNDY3LDAuNTI0LDAuMDEsMC43MTZMMjYuNTYsNTAuODFjMC40NzcsMC4xOTEsMS4yNTEsMC4xOTEsMS43MjksMEw1NC40ODgsNDAuMzNDNTQuOTY0LDQwLjEzOSw1NC45NjksMzkuODE3LDU0LjQ5NywzOS42MTR6Ii8+PHBhdGggZD0iTTU0LjQ5NywyNy41MTJsLTEwLjM2NC00LjQ5MWwtMTQuOTE2LDUuOTY2Yy0wLjUzNiwwLjIxNS0xLjE2NSwwLjMyMS0xLjc5MiwwLjMyMWMtMC42MjgsMC0xLjI1Ni0wLjEwNi0xLjc5My0wLjMyMWwtMTQuOTE4LTUuOTY2TDAuMzUxLDI3LjUxMmMtMC40NzIsMC4yMDMtMC40NjcsMC41MjMsMC4wMSwwLjcxNkwyNi41NiwzOC43MDZjMC40NzcsMC4xOSwxLjI1MSwwLjE5LDEuNzI5LDBsMjYuMTk5LTEwLjQ3OUM1NC45NjQsMjguMDM2LDU0Ljk2OSwyNy43MTYsNTQuNDk3LDI3LjUxMnoiLz48cGF0aCBkPSJNMC4zNjEsMTYuMTI1bDEzLjY2Miw1LjQ2NWwxMi41MzcsNS4wMTVjMC40NzcsMC4xOTEsMS4yNTEsMC4xOTEsMS43MjksMGwxMi41NDEtNS4wMTZsMTMuNjU4LTUuNDYzYzAuNDc3LTAuMTkxLDAuNDgtMC41MTEsMC4wMS0wLjcxNkwyOC4yNzcsNC4wNDhjLTAuNDcxLTAuMjA0LTEuMjM2LTAuMjA0LTEuNzA4LDBMMC4zNTEsMTUuNDFDLTAuMTIxLDE1LjYxNC0wLjExNiwxNS45MzUsMC4zNjEsMTYuMTI1eiIvPjwvZz48L2c+PC9nPjxnPjwvZz48Zz48L2c+PGc+PC9nPjxnPjwvZz48Zz48L2c+PGc+PC9nPjxnPjwvZz48Zz48L2c+PGc+PC9nPjxnPjwvZz48Zz48L2c+PGc+PC9nPjxnPjwvZz48Zz48L2c+PGc+PC9nPjwvc3ZnPg==);
        background-position: center;
        background-repeat: no-repeat;
        background-size: 70%;
      }
    </style>
  </head>

  <body>
    <h2 style="font-family: sans-serif">Mapbox style switcher</h2>
    <div id="map" style="height: 60%"></div>
    <script>
      const map = new maplibregl.Map({
        container: 'map',
        style:
          'https://api.maptiler.com/maps/streets/style.json?key=' +
          config.MAPTILER_TOKEN,
        center: [-122.4194, 37.7788],
        zoom: 12,
      })

      var styles = (MapboxStyleDefinition = [
        {
          title: 'Basic',
          uri:
            'https://api.maptiler.com/maps/streets/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Light',
          uri:
            'https://api.maptiler.com/maps/bright/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Satellite Hybrid',
          uri:
            'https://api.maptiler.com/maps/hybrid/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
        {
          title: 'Outdoors',
          uri:
            'https://api.maptiler.com/maps/outdoor/style.json?key=' +
            config.MAPTILER_TOKEN,
        },
      ])

      map.addControl(new MapboxStyleSwitcherControl(styles))
    </script>
  </body>
</html>
```

[Demo](https://astridx.github.io/maplibreexamples/plugins/maplibre-style-switcher.html)  
[Quellcode](https://github.com/astridx/maplibreexamples/blob/main/plugins/maplibre-style-switcher.html)  
[Gatsby Starter mit dieser Funktion](https://github.com/astridx/gatsby-starter-mapbox-examples) - [Gatsby Starter Demo](https://astridx.github.io/gatsbystarter/gatsby-starter-mapbox-examples/)
