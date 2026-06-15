---
{"dg-publish":true,"permalink":"/notes/vae-builder-ibek-rtems-ioc-migration/","title":"VAE Builder ibek manual build RTEMS IOC build system migration","tags":["kind/concept","domain/embedded-systems","domain/controls","domain/accelerator"],"dg-note-properties":{"title":"VAE Builder ibek manual build RTEMS IOC build system migration","type":"note","created":"2026-06-01","updated":"2026-06-01","source":"import","tags":["kind/concept","domain/embedded-systems","domain/controls","domain/accelerator"],"summary":"Whiteboard architecture diagram documenting migration from legacy XML/Toc IOC build to ibek-driven ioc.yaml for RTEMS-based EPICS IOCs, with TFTP+NFSv2 boot infrastructure.","personal":false}}
---


# VAE Builder ibek manual build RTEMS: IOC build system migration

![vae-builder-ibek-rtems-ioc-migration.jpg](/img/user/raw/assets/vae-builder-ibek-rtems-ioc-migration.jpg)
<style> .container {font-family: sans-serif; text-align: center;} .button-wrapper button {z-index: 1;height: 40px; width: 100px; margin: 10px;padding: 5px;} .excalidraw .App-menu_top .buttonList { display: flex;} .excalidraw-wrapper { height: 800px; margin: 50px; position: relative;} :root[dir="ltr"] .excalidraw .layer-ui__wrapper .zen-mode-transition.App-menu_bottom--transition-left {transform: none;} </style><script src="https://cdn.jsdelivr.net/npm/react@17/umd/react.production.min.js"></script><script src="https://cdn.jsdelivr.net/npm/react-dom@17/umd/react-dom.production.min.js"></script><script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@excalidraw/excalidraw@0/dist/excalidraw.production.min.js"></script><div id="vae-builder-ibek-rtems-ioc-migrationexcalidraw.md1"></div><script>(function(){const InitialData={"type":"excalidraw","version":2,"source":"thoth","elements":[{"id":"5cb43045","type":"text","x":420,"y":40,"width":480,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1335939822,"version":1,"versionNonce":1040221618,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"VME Builder -> ibek + manual build RTEMS","rawText":"VME Builder -> ibek + manual build RTEMS","originalText":"VME Builder -> ibek + manual build RTEMS","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"67f46d2b","type":"text","x":300,"y":140,"width":36,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":848716819,"version":1,"versionNonce":1803802224,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"old","rawText":"old","originalText":"old","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"6d61e637","type":"text","x":40,"y":190,"width":84,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":156421886,"version":1,"versionNonce":1900253920,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"Support","rawText":"Support","originalText":"Support","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"555c66a8","type":"text","x":460,"y":175,"width":36,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1486871735,"version":1,"versionNonce":1869024192,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"Ioc","rawText":"Ioc","originalText":"Ioc","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"e8e62735","type":"text","x":960,"y":135,"width":216,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1781061890,"version":1,"versionNonce":866158877,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"ioc VA - RTEMS new","rawText":"ioc VA - RTEMS new","originalText":"ioc VA - RTEMS new","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"82ade41f","type":"text","x":1700,"y":175,"width":144,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":104643829,"version":1,"versionNonce":1518295393,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"TFTP + NFSv2","rawText":"TFTP + NFSv2","originalText":"TFTP + NFSv2","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"xml","type":"rectangle","x":40,"y":250,"width":150,"height":110,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":{"type":3},"seed":49495369,"version":1,"versionNonce":1304414189,"isDeleted":false,"boundElements":[{"type":"text","id":"c6a034e1"},{"type":"arrow","id":"a1"},{"type":"arrow","id":"a12"}],"updated":1,"link":null,"locked":false},{"id":"c6a034e1","type":"text","x":97,"y":292.5,"width":36,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":504980036,"version":1,"versionNonce":948406608,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"xml","rawText":"xml","originalText":"xml","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"xml","lineHeight":1.25,"autoResize":true},{"id":"ioc1","type":"rectangle","x":390,"y":230,"width":260,"height":350,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":{"type":3},"seed":923882549,"version":1,"versionNonce":1916521671,"isDeleted":false,"boundElements":[{"type":"text","id":"24f8d1b2"},{"type":"arrow","id":"a1"},{"type":"arrow","id":"a5"}],"updated":1,"link":null,"locked":false},{"id":"24f8d1b2","type":"text","x":395,"y":392.5,"width":250,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1543582592,"version":1,"versionNonce":1527356577,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"Db/\n Subst\nConfigure/\n RELEASE\nsrc/\n makefile\niocBoot/\n st.cmd","rawText":"Db/\n Subst\nConfigure/\n RELEASE\nsrc/\n makefile\niocBoot/\n st.cmd","originalText":"Db/\n Subst\nConfigure/\n RELEASE\nsrc/\n makefile\niocBoot/\n st.cmd","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"ioc1","lineHeight":1.25,"autoResize":true},{"id":"c322466c","type":"text","x":270,"y":300,"width":144,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1626234528,"version":1,"versionNonce":783511651,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"ipac\nmvs937a","rawText":"ipac\nmvs937a","originalText":"ipac\nmvs937a","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"0529a29a","type":"text","x":740,"y":270,"width":24,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1370690380,"version":1,"versionNonce":1512616436,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"db","rawText":"db","originalText":"db","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"bd9ea254","type":"text","x":740,"y":350,"width":120,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":101135878,"version":1,"versionNonce":501959050,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"dbd\nbinary","rawText":"dbd\nbinary","originalText":"dbd\nbinary","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"2f8f0f52","type":"text","x":840,"y":205,"width":60,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":511701308,"version":1,"versionNonce":1577578899,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"track","rawText":"track","originalText":"track","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"iocnew","type":"rectangle","x":950,"y":230,"width":280,"height":300,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":{"type":3},"seed":1929158060,"version":1,"versionNonce":593418296,"isDeleted":false,"boundElements":[{"type":"text","id":"e4937595"},{"type":"arrow","id":"a5"}],"updated":1,"link":null,"locked":false},{"id":"e4937595","type":"text","x":955,"y":367.5,"width":270,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1282852305,"version":1,"versionNonce":539427105,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"Configure/\n RELEASE\nsrc/\n makefile\nStreamDevice\n copy","rawText":"Configure/\n RELEASE\nsrc/\n makefile\nStreamDevice\n copy","originalText":"Configure/\n RELEASE\nsrc/\n makefile\nStreamDevice\n copy","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"iocnew","lineHeight":1.25,"autoResize":true},{"id":"89076b08","type":"text","x":1010,"y":330,"width":264,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1786522088,"version":1,"versionNonce":259719207,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"all protos go in\ndeti/","rawText":"all protos go in\ndeti/","originalText":"all protos go in\ndeti/","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"a5b65284","type":"text","x":870,"y":290,"width":144,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":611598695,"version":1,"versionNonce":56806713,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"ipac\nmvs937a","rawText":"ipac\nmvs937a","originalText":"ipac\nmvs937a","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"c8eeddb6","type":"text","x":1280,"y":310,"width":120,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":40377666,"version":1,"versionNonce":555236524,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"dbd\nbinary","rawText":"dbd\nbinary","originalText":"dbd\nbinary","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"iocfinal","type":"rectangle","x":1620,"y":250,"width":280,"height":380,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":{"type":3},"seed":1472804903,"version":1,"versionNonce":126176714,"isDeleted":false,"boundElements":[{"type":"text","id":"fd589dd0"}],"updated":1,"link":null,"locked":false},{"id":"fd589dd0","type":"text","x":1742,"y":427.5,"width":36,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":472044415,"version":1,"versionNonce":1414301499,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"ioc","rawText":"ioc","originalText":"ioc","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"iocfinal","lineHeight":1.25,"autoResize":true},{"id":"f1b03cd5","type":"text","x":1440,"y":420,"width":36,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1844833317,"version":1,"versionNonce":190442120,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"msi","rawText":"msi","originalText":"msi","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"1fa50fcb","type":"text","x":1500,"y":360,"width":24,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":431201878,"version":1,"versionNonce":265394687,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"db","rawText":"db","originalText":"db","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"iocyaml","type":"rectangle","x":940,"y":520,"width":330,"height":340,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":{"type":3},"seed":1980891528,"version":1,"versionNonce":6144683,"isDeleted":false,"boundElements":[{"type":"text","id":"70a40f38"},{"type":"arrow","id":"a12"}],"updated":1,"link":null,"locked":false},{"id":"70a40f38","type":"text","x":945,"y":677.5,"width":320,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":303243180,"version":1,"versionNonce":1843183879,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"ioc.yaml\n - type: ipac.Hyg8002\n - type: mvs937a.\n - env\n   st","rawText":"ioc.yaml\n - type: ipac.Hyg8002\n - type: mvs937a.\n - env\n   st","originalText":"ioc.yaml\n - type: ipac.Hyg8002\n - type: mvs937a.\n - env\n   st","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"iocyaml","lineHeight":1.25,"autoResize":true},{"id":"d113416e","type":"text","x":1310,"y":600,"width":60,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":22219165,"version":1,"versionNonce":1062508772,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"subst","rawText":"subst","originalText":"subst","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"8ee0ee4c","type":"text","x":1310,"y":700,"width":72,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1344349693,"version":1,"versionNonce":1725756155,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"st.cmd","rawText":"st.cmd","originalText":"st.cmd","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"4c01124b","type":"text","x":250,"y":620,"width":912,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1915135459,"version":1,"versionNonce":279425595,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"• builder2ibek from builder.xml to ioc.yaml\n• ioc.yaml passed to RTEMS proxy","rawText":"• builder2ibek from builder.xml to ioc.yaml\n• ioc.yaml passed to RTEMS proxy","originalText":"• builder2ibek from builder.xml to ioc.yaml\n• ioc.yaml passed to RTEMS proxy","fontSize":20,"fontFamily":1,"textAlign":"left","verticalAlign":"middle","baseline":17,"containerId":null,"lineHeight":1.25,"autoResize":true},{"id":"a1","type":"arrow","x":197.77,"y":325.44,"width":184.47,"height":45.55,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1630209767,"version":1,"versionNonce":1496064579,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"points":[[0,0],[184.47,45.55]],"lastCommittedPoint":null,"startBinding":{"elementId":"xml","focus":0,"gap":8},"endBinding":{"elementId":"ioc1","focus":0,"gap":8},"startArrowhead":null,"endArrowhead":"arrow"},{"id":"a5","type":"arrow","x":657.99,"y":398.95,"width":284.02,"height":12.46,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1899931089,"version":1,"versionNonce":1498855535,"isDeleted":false,"boundElements":[{"type":"text","id":"2379642b"}],"updated":1,"link":null,"locked":false,"points":[[0,0],[284.02,-12.46]],"lastCommittedPoint":null,"startBinding":{"elementId":"ioc1","focus":0,"gap":8},"endBinding":{"elementId":"iocnew","focus":0,"gap":8},"startArrowhead":null,"endArrowhead":"arrow"},{"id":"2379642b","type":"text","x":770,"y":380.22,"width":60,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":531103839,"version":1,"versionNonce":1945170064,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"track","rawText":"track","originalText":"track","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"a5","lineHeight":1.25,"autoResize":true},{"id":"a12","type":"arrow","x":197.46,"y":337.07,"width":735.09,"height":285.87,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":1721662915,"version":1,"versionNonce":1314377630,"isDeleted":false,"boundElements":[{"type":"text","id":"5acf86bc"}],"updated":1,"link":null,"locked":false,"points":[[0,0],[735.09,285.87]],"lastCommittedPoint":null,"startBinding":{"elementId":"xml","focus":0,"gap":8},"endBinding":{"elementId":"iocyaml","focus":0,"gap":8},"startArrowhead":null,"endArrowhead":"arrow"},{"id":"5acf86bc","type":"text","x":493,"y":467.5,"width":144,"height":25,"angle":0,"strokeColor":"#1e1e1e","backgroundColor":"transparent","fillStyle":"solid","strokeWidth":2,"strokeStyle":"solid","roughness":1,"opacity":100,"groupIds":[],"frameId":null,"roundness":null,"seed":976863559,"version":1,"versionNonce":1493218163,"isDeleted":false,"boundElements":null,"updated":1,"link":null,"locked":false,"text":"builder2ibek","rawText":"builder2ibek","originalText":"builder2ibek","fontSize":20,"fontFamily":1,"textAlign":"center","verticalAlign":"middle","baseline":17,"containerId":"a12","lineHeight":1.25,"autoResize":true}],"appState":{"gridSize":null,"viewBackgroundColor":"#ffffff","zoom":{"value":1}},"files":{}};InitialData.scrollToContent=true;App=()=>{const e=React.useRef(null),t=React.useRef(null),[n,i]=React.useState({width:void 0,height:void 0});return React.useEffect(()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height});const e=()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height})};return window.addEventListener("resize",e),()=>window.removeEventListener("resize",e)},[t]),React.createElement(React.Fragment,null,React.createElement("div",{className:"excalidraw-wrapper",ref:t},React.createElement(ExcalidrawLib.Excalidraw,{ref:e,width:n.width,height:n.height,initialData:InitialData,viewModeEnabled:!0,zenModeEnabled:!0,gridModeEnabled:!1})))},excalidrawWrapper=document.getElementById("vae-builder-ibek-rtems-ioc-migrationexcalidraw.md1");ReactDOM.render(React.createElement(App),excalidrawWrapper);})();</script>

Whiteboard diagram (captured 2026-02-11) comparing three build paths for [[RTEMS\|RTEMS]]-based [[EPICS\|EPICS]] IOCs, illustrating the migration from a legacy XML/Toc system to an [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]]-driven `ioc.yaml` approach.

## Three swim lanes

### 1. OLD / TOC path

- Input: XML
- Toc box generates: `Db/subst`, `configure/RELEASE`, `src/makefile`, `iacBoot/st.cmd`
- Annotated: `ipc Nts193a`
- Outputs: `db`, `dbd/binary`

### 2. HACK / new path (centre)

- Labelled *hack*; receives `ipc VA - RTEMSNew` from the OLD path
- Contains `configure/RELEASE`, `src/makefile`
  - Notes: all probes go to deb, StreamDevice copy
- Annotated: `ipc Nts193a`
- Outputs: `dbd/binary` then `msi` then `db`
- `db` fed into a right-side ioc box (TFTP + NFSv2)

### 3. BUILDER / IBEK path (bottom)

- `builderZIbek` arrow leads to `ioc.yaml` with entries:
  - `- type: ipac.Vig8002`
  - `- type: nts193a.`
  - `- env ST`
- Branches to: `subst`, `st.cmd`
- Red bullet notes:
  - builderZibek from builderxml to ioc.yaml
  - ioc.yaml passed to RTSHS proxy

## Boot infrastructure

The right-side ioc box uses TFTP + NFSv2 for booting RTEMS targets.

## Key concepts

| Term | Role |
|---|---|
| `ioc.yaml` | Declarative IOC definition consumed by ibek |
| `builderZibek` | Tool translating legacy builderXML to ioc.yaml |
| `RTSHS proxy` | Proxy that receives and acts on ioc.yaml for RTEMS |
| `configure/RELEASE` | EPICS build system dependency file |
| `dbd` | EPICS database definition binary |
| `msi` | Macro substitution tool producing final .db |
| `StreamDevice` | EPICS device support library, copied in hack path |
| `Vig8002` | IPAC carrier board type (ipac.Vig8002) |
| `Nts193a` | IOC/device type present in both old and new paths |

## Related pages

- [[notes/ibek-runtime-support-helm-architecture\|ibek-runtime-support-helm-architecture]]
- [[notes/ibck-runtime-support-directory-structure\|ibck-runtime-support-directory-structure]]
- [[notes/ibch-runtime-support-helm-shared-schema-architecture\|ibch-runtime-support-helm-shared-schema-architecture]]
- [[notes/beamline-control-system-roadmap-whiteboard-2025-2028\|beamline-control-system-roadmap-whiteboard-2025-2028]]
- [[notes/ixx-sources-to-ixx-apis-architecture-diagram\|ixx-sources-to-ixx-apis-architecture-diagram]]

## Extracted text

VAE Builder → ibek + manual build RTEMS

ipc VA - RTEMSNew

old

TFTP + NFSv2

Support

Toc

hack

ioc

xml → Db/
       subst
       configure/
       RELEASE
       src/
         makefile
       iacBoot/
         st.cmd

→ db

ipc
Nts193a

→ dbd
  binary

configure/
RELEASE
src/
  makefile
    all probes go to
    deb
    StreamDevice
    copy

ipc
Nts193a

→ dbd
  binary

→ msi

→ db

builderZIbek

→ ioc.yaml
  - type: ipac.Vig8002
  - type: nts193a.

→ subst

→ st.cmd

- env
  ST

• builderZibek from builderxml to ioc.yaml
• ioc.yaml passed to RTSHS proxy
