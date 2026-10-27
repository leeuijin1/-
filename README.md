<!DOCTYPE html>
<html lang="ko"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>푸리에 변환 시각화</title>
<style>
:root{--bg:#f6f6f3;--panel:#fff;--fg:#1c1c1e;--mut:#6b6b70;--grid:#dcdcd6;--acc:#c98a00;--red:#e0323c;--bd:#d8d8d2;color-scheme:light;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#101114;--panel:#1a1b20;--fg:#ececf0;--mut:#9a9aa3;--grid:#33343b;--acc:#f2c744;--red:#ff5560;--bd:#30313a;color-scheme:dark}}
:root[data-theme="dark"]{--bg:#101114;--panel:#1a1b20;--fg:#ececf0;--mut:#9a9aa3;--grid:#33343b;--acc:#f2c744;--red:#ff5560;--bd:#30313a;color-scheme:dark}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--fg);font:14px/1.5 -apple-system,"Apple SD Gothic Neo","Noto Sans KR","Malgun Gothic",sans-serif;padding:10px;display:flex;flex-direction:column;gap:10px;height:100dvh;overflow:hidden}[hidden]{display:none!important}
.card{background:var(--panel);border:1px solid var(--bd);border-radius:12px;padding:8px;display:flex;flex-direction:column;min-height:0}
.layout{display:grid;grid-template-columns:1fr 140px;gap:10px;flex:1.3 1 0;min-height:0}.layout>*{min-height:0}
.main{display:flex;flex-direction:column;gap:10px;min-width:0}.f1{flex:1 1 0}.f2{flex:1.5 1 0}
.side{display:flex;flex-direction:column;gap:4px;overflow:auto}
.side h3{margin:0;font-size:13px;text-align:center}
.nb{display:flex;align-items:center;gap:6px;padding:2px 8px;border:1px solid var(--bd);border-radius:8px;background:var(--bg);color:var(--fg);cursor:pointer;font:inherit;font-size:12px;text-align:left}
.nb i{width:10px;height:10px;border-radius:50%;flex:none;background:var(--grid)}
.nb small{margin-left:auto;color:var(--mut);font-size:11px}
.nb.on{border-color:var(--c);background:color-mix(in srgb,var(--c) 18%,var(--panel))}
.nb.on i{background:var(--c)}
.nb:disabled{opacity:.6;cursor:default}
button,select,input[type=number]{font:inherit;background:var(--bg);color:var(--fg);border:1px solid var(--bd);border-radius:8px;padding:4px 10px;cursor:pointer}
input[type=number]{width:90px;cursor:text}
button.on{background:var(--acc);color:#111;border-color:var(--acc)}
.cv{position:relative;flex:1 1 0;min-height:0}canvas{position:absolute;inset:0;width:100%;height:100%}
#stack{flex:1 1 0;min-height:0;display:flex;flex-direction:column;gap:2px}#stack .cv{min-height:30px}
.ph{color:var(--mut);text-align:center;padding:26px 0}
.row{display:flex;gap:8px;align-items:center;flex-wrap:wrap;margin:3px 0}
.row
