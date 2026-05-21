# Handoff — MDtoDOCX v1.3
**Fecha:** 2026-05-21  
**Proyecto:** MDtoDOCX  
**Archivo entregado:** `index_mdtodocx_v1.3.html`  
**Estado:** ✅ Funcional

---

## Qué se hizo en esta sesión

Fix de HTML entities que aparecían como texto literal en el DOCX generado.

| Síntoma | Causa | Fix |
|---|---|---|
| `&amp;` en el DOCX | `marked.js` escapa entities en los tokens `.text` | `decodeHtmlEntities()` antes de cada `TextRun` |
| `&quot;` en el DOCX | Ídem | Ídem |
| Potencial `&lt;` / `&gt;` | Ídem | Cubierto por el mismo fix |

---

## Cambios respecto a v1.2

### Nueva función helper
```js
function decodeHtmlEntities(str) {
  if (!str) return str;
  const txt = document.createElement('textarea');
  txt.innerHTML = str;
  return txt.value;
}
```
Usa el parser nativo del browser vía `textarea.innerHTML`. Robusto, sin regex frágiles, cero dependencias.

### Puntos de aplicación (todos los `TextRun` que reciben texto de tokens)
| Ubicación | Campo decodificado |
|---|---|
| `inlineRuns` — `text` / `escape` | `t.text`, `t.raw` |
| `inlineRuns` — `strong` / `em` / `del` | `s.text`, `s.raw` |
| `inlineRuns` — `codespan` | `t.text` |
| `inlineRuns` — `link` | `t.text`, `t.href` |
| `inlineRuns` — `default` | `t.raw` |
| `tokToDocx` — `code` | `tok.text` (bloque completo antes del `.split('\n')`) |
| `tokToDocx` — `table` headers | `cell.text` |

---

## Stack (sin cambios)

```
marked.js       → CDN cdnjs v9.1.6
docx@7.1.0      → CDN unpkg (global window.docx, acceso inline en funciones)
FileSaver.js    → CDN cdnjs v1.3.8
```

---

## Historial de versiones

| Versión | Problema | Fix |
|---|---|---|
| v1.0 | `docx is not defined` — unpkg bloqueado por `file://` | — |
| v1.1 | `Cannot destructure 'Document' of 'D'` — desestructuración prematura | — |
| v1.2 | ✅ Funcional — base estable | Stack oficial + acceso inline al global |
| v1.3 | HTML entities literales en DOCX | `decodeHtmlEntities()` en todos los TextRun |

---

## Pendientes / v2.0

- [ ] Soporte imágenes embebidas en MD (`![alt](src)`)
- [ ] Listas numeradas (requiere `numbering` config en docx.js)
- [ ] Ignorar / mapear frontmatter YAML
- [ ] Modo batch (múltiples archivos → ZIP)
- [ ] Opción: tabla de contenidos automática
- [ ] Subir a GitHub Pages y probar PWA install

---

## Para retomar

1. Abrir `index_mdtodocx_v1.3.html` como referencia
2. Próxima versión: `index_mdtodocx_v2.0.html`
3. Cualquier feature nueva: **conversar diseño antes de codear**
