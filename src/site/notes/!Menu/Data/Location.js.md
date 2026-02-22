---
{"dg-publish":true,"permalink":"/menu/data/location-js/"}
---

const This = dv.current();

// =======================
// Helpers
// =======================
function renderList(value) {
    if (!value) return "—";
    if (Array.isArray(value)) return value.join(", ");
    return value;
}

// =======================
// Main Wrapper
// =======================
const wrapper = dv.el("div", "", {
    attr: {
        style: `
            display: flex;
            flex-direction: column;
            gap: 1.5em;
            width: 100%;
        `
    }
});

dv.span("![[Characters.base#Location]]")

