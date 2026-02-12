# Промпт для продолжения проекта конвертации обложек плейлистов

## Задача

Я загружаю HTML-файлы обложек музыкальных плейлистов и мне нужно конвертировать их в PNG. Каждый файл — это самодостаточная HTML-страница с CSS и inline SVG, содержащая обложку 600×600px для Tidal-плейлиста конкретного музыкального жанра.

## Технический процесс

**Рендеринг:** Node.js + Playwright (chromium), headless.

```javascript
const { chromium } = require("playwright");
(async () => {
  const browser = await chromium.launch({ args: ["--no-sandbox"] });
  const files = ["genre-name-cover"]; // список файлов без расширения
  for (const name of files) {
    const page = await browser.newPage({ viewport: { width: 800, height: 800, deviceScaleFactor: 2 } });
    await page.goto("file:///home/claude/" + name + ".html", { waitUntil: "networkidle" });
    await page.evaluateHandle("document.fonts.ready");
    await page.waitForTimeout(1500);
    const cover = await page.locator(".cover");
    await cover.screenshot({ path: "/home/claude/" + name + ".png", type: "png" });
    console.log("Done: " + name);
    await page.close();
  }
  await browser.close();
})();
```

**Параметры:**
- Viewport: 800×800, deviceScaleFactor: 2 (итоговый PNG ~1200×1200 retina)
- Скриншот элемента `.cover` (600×600 CSS → 1200×1200 PNG)
- Ожидание 1500ms для загрузки шрифтов Google (Anton, DM Mono)
- Файлы копируются из `/mnt/user-data/uploads/` в `/home/claude/`, рендерятся, затем PNG копируются в `/mnt/user-data/outputs/`

**Порядок действий:**
1. Скопировать HTML из uploads в рабочую директорию
2. Запустить Node.js скрипт (все файлы в одном запуске)
3. Скопировать готовые PNG в `/mnt/user-data/outputs/`
4. Просмотреть каждый PNG через `view` для визуальной проверки
5. Выдать файлы через `present_files`

## Что уже готово (112 обложек)

acid-techno, aggrotech, ambient-idm, ambient-techno, apocalyptic-folk, arena-rock, art-rock, atmospheric-black-metal, avant-garde-rock, avant-pop, berlin-techno, big-beat, blackgaze, braindance, breakbeat, classic-ambient, classic-electro, classic-idm, classic-punk, classic-rock, coldwave, college-rock, cool-jazz, dance-pop, dance-punk, dark-electro, darkwave, deathcore, deep-house, deep-techno, detroit-techno, djent, doom-metal, dream-pop, drum-and-bass, dub-techno, ebm, electro-idm, electroclash, electropop, experimental-hip-hop, experimental-techno, folk-metal, folk-rock, forest-folk, futurepop, garage-rock, glitch, gothic-rock, grindcore, groove-metal, happy-hardcore, hard-rock, hard-trance, hardcore-punk, heavy-metal, hyperpop, hypnotic-techno, indie-folk, indie-rock, indietronica, industrial-ambient, industrial-metal, industrial-noise, industrial-techno, jangle-pop, jungle, krautrock, leftfield, mainstream-hardcore, mathcore, melodic-black-metal, melodic-death-metal, melodic-hardcore, melodic-techno, metalcore, minimal-house, modal-jazz, modern-prog-rock, neo-prog, neoclassical-folk, new-wave, nu-metal, pagan-folk, pop-punk, post-hardcore, post-idm, post-metal, post-punk-revival, post-rock, power-electronics, power-metal, progressive-house, progressive-techno, progressive-trance, psybient, rave-techno, raw-black-metal, screamo, shoegaze, slowcore, sludge-metal, soft-rock, space-rock, symphonic-black-metal, symphonic-metal, symphonic-prog, tech-house, technical-death-metal, thrash-metal, trip-hop, underground-hip-hop

## Формат ответа

После рендеринга — короткий художественный комментарий по каждой обложке (основная форма, ключевые параметры дизайна: grain%, glow, blur, displacement scale, BPM spacing, цветовая температура, какие жанровые пары/семейства формирует). Без лишних слов — конкретика и дизайн-анализ.

Язык общения: русский/английский mix, технические термины на английском.
