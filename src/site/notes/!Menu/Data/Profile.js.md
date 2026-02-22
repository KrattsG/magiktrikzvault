---
{"dg-publish":true,"permalink":"/menu/data/profile-js/"}
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

// =======================
// TOP: Character Summary
// =======================
const header = dv.el("div", "", {
    attr: {
        style: `
            display: flex;
            gap: 1em;
            padding: 1em;
            border-radius: 12px;
            border: 1px solid #444;
            color: white;
            align-items: center;
        `
    }
});

// Profile Image
const img = dv.el("img", "", {
    attr: {
        src: This.Profile ?? "https://via.placeholder.com/160",
        style: `
            width: 160px;
            height: 100%;
            object-fit: cover;
            border-radius: 10px;
            cursor: pointer;
        `
    }
});

img.addEventListener("click", () => {
    const baseURL = "https://pt.pinterest.com/slitle_fdois/magik-trikz/";
    const characterName = This.file.name.toLowerCase().replace(/\s+/g, "-");
    window.open(`${baseURL}${characterName}/`, "_blank");
});

const info = dv.el("div", "", {});
info.appendChild(dv.el("div", This.file.link, { attr: { style: "font-size: 24px; font-weight: bold;" } }));
info.appendChild(dv.el("div", `Creator: ${renderList(This.Creator)}`));
info.appendChild(dv.el("div", `Location: ${renderList(This.file.frontmatter["Location :"])}`));

header.append(img, info);
wrapper.appendChild(header);

// =======================
// Page Carousel
// =======================
const carousel = dv.el("div", "", {
    attr: {
        style: `
            display: flex;
            overflow-x: auto;
            scroll-snap-type: x mandatory;
            width: 100%;
            max-height: 65vh;
            overscroll-behavior-x: contain;
        `
    }
});

// =======================
// Collect Pages + Relations
// =======================
const pages = Object.entries(This) .filter(([key]) => /^page_\d+$/.test(key)) .sort(([a], [b]) => Number(a.split("_")[1]) - Number(b.split("_")[1])) .map(([key, value]) => ({ title: value.title ?? "Untitled", text: value.text ?? "" }))


// Add Relations page at the end
pages.push({
    title: "Character Relations",
    text: "![[Characters.base#Relations]]"
});

// =======================
// Page Factory
// =======================
function createPage(page) {
    const pageDiv = document.createElement("div");

    Object.assign(pageDiv.style, {
        minWidth: "100%",
        padding: "1.5em",
        boxSizing: "border-box",
        scrollSnapAlign: "start",
        color: "#fff",
        display: "flex",
        flexDirection: "column",
        gap: "1em",
        backgroundColor: "#222",
    });

    const title = document.createElement("div");
    title.textContent = page.title;
    title.style.fontSize = "22px";
    title.style.fontWeight = "bold";
    title.style.borderBottom = "1px solid #555";
    title.style.paddingBottom = "0.5em";

    pageDiv.appendChild(title);
	pageDiv.appendChild(dv.span(page.text.trim()));

    return pageDiv;
}

// Render all pages
carousel.innerHTML = "";
for (const page of pages) {
    carousel.appendChild(createPage(page));
}

wrapper.appendChild(carousel);

// =======================
// Horizontal Wheel Scrolling
// =======================
setTimeout(() => {
    let idx = 0;

    carousel.addEventListener(
        "wheel",
        e => {
            if (Math.abs(e.deltaY) < Math.abs(e.deltaX)) return;
            e.preventDefault();

            const panels = carousel.children.length;
            if (!panels) return;

            idx = e.deltaY > 0 ? Math.min(idx + 1, panels - 1) : Math.max(idx - 1, 0);

            carousel.scrollTo({
                left: idx * carousel.clientWidth,
                behavior: "smooth"
            });
        },
        { passive: false }
    );
}, 0);