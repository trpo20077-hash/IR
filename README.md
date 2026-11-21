<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>صقور السماء — تجربة</title>
<link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;800&family=Teko:wght@600;700&display=swap" rel="stylesheet">
<style>
/* VOICE: لعبة حربية حماسية
   IDEA: قاعدة جوية وقمرة طيار فوق صحراء
   PICTURE: المشهد ثلاثي الأبعاد نفسه
   COLOUR/TYPE: سماء رمادية، برتقالي للأزرار، أحمر للخطر، Cairo
   LAYOUT: جويستك يسار، أزرار القتال يمين، رادار فوق */
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent;user-select:none;-webkit-user-select:none}
html,body{width:100%;height:100%;overflow:hidden;background:#000;touch-action:none;font-family:'Cairo',system-ui,sans-serif;color:#f4f1ea}
canvas#gl{position:fixed;inset:0;width:100%;height:100%;display:block}
#hud{position:fixed;inset:0;pointer-events:none}
#hud .tap{pointer-events:auto;touch-action:none}
.bar{height:8px;border-radius:4px;background:rgba(0,0,0,.45);overflow:hidden}
.bar>i{display:block;height:100%;width:100%;background:#e8c547;transition:width .2s}
#tl{position:absolute;left:16px;top:58px;width:240px}
#feed{direction:rtl;text-align:right;font-size:12px;line-height:1.55;text-shadow:0 1px 2px #000}
#feed b{color:#7dff9a}
#top{position:absolute;left:50%;top:6px;transform:translateX(-50%);display:flex;gap:12px;align-items:center;font-weight:700}
#top .pill{background:rgba(0,0,0,.38);padding:0 10px;border-radius:6px;font-size:14px;white-space:nowrap}
#coins{font-size:19px;color:#ffe08a}
#bPause{width:40px;height:40px;border-radius:8px;border:1px solid rgba(255,255,255,.4);background:rgba(0,0,0,.38);color:#fff;font-size:16px;font-weight:800}
#radar{position:absolute;right:16px;top:10px;width:96px;height:96px;border-radius:50%;border:2px solid #3fbf5f;background:rgba(20,60,30,.4)}
#cross{position:absolute;width:40px;height:40px;margin:-20px 0 0 -20px;left:50%;top:50%}
.lk{position:absolute;border:2px solid rgba(255,90,70,.8);width:46px;height:46px;margin:-23px 0 0 -23px;display:none}
.lk.far{border-color:rgba(255,255,255,.55);width:30px;height:30px;margin:-15px 0 0 -15px}
.lk span{position:absolute;top:-19px;left:50%;transform:translateX(-50%);white-space:nowrap;font-size:11px;font-weight:700;text-shadow:0 1px 2px #000}
.lk i{position:absolute;left:0;bottom:-9px;height:4px;background:#ff4a3a}
.lk.cand{border-color:#ffd447}
.lk.lock{border-color:#ff4a3a;border-width:3px}
.lk.lock span{color:#ff6b5b}
#warn{position:absolute;left:50%;top:22%;transform:translateX(-50%);font-size:22px;font-weight:800;color:#fff;background:#d6332a;padding:2px 18px;border-radius:8px;display:none;direction:rtl;white-space:nowrap}
#big{position:absolute;left:50%;top:34%;transform:translateX(-50%);font-size:28px;font-weight:800;text-shadow:0 2px 6px #000;opacity:0;transition:opacity .4s;direction:rtl;white-space:nowrap}
#joy{position:absolute;left:34px;bottom:26px;width:140px;height:140px;border-radius:50%;border:5px solid #ff8a3d;background:rgba(0,0,0,.12)}
#knob{position:absolute;left:50%;top:50%;width:52px;height:52px;margin:-26px 0 0 -26px;border-radius:50%;border:2px solid rgba(255,255,255,.75);background:rgba(255,255,255,.22)}
#thr{position:absolute;right:16px;bottom:26px;width:40px;height:160px;border-radius:9px;background:rgba(0,0,0,.45);border:1px solid rgba(255,255,255,.3)}
#thrFill{position:absolute;left:4px;right:4px;bottom:4px;max-height:calc(100% - 8px);border-radius:5px;background:#c94a3a}
#thrKnob{position:absolute;left:-7px;right:-7px;height:14px;margin-bottom:-7px;border-radius:4px;background:#2b2f36;border:1px solid #9aa0a8}
.btn{position:absolute;border-radius:50%;display:flex;align-items:center;justify-content:center;border:3px solid #ff8a3d;background:rgba(40,44,52,.85)}
.btn svg{width:46%;height:46%}
.btn:active,.btn.on{filter:brightness(1.35)}
#bFire{right:72px;bottom:26px;width:84px;height:84px;background:#d6332a;border-color:#fff}
#bSw{right:170px;bottom:30px;width:62px;height:62px}
#bFlr{right:170px;bottom:112px;width:58px;height:58px}
#bCam{right:246px;bottom:30px;width:58px;height:58px}
.cnt{position:absolute;bottom:-4px;right:-6px;min-width:24px;height:22px;border-radius:11px;background:#14181f;border:1px solid #ff8a3d;font-size:12px;font-weight:800;display:flex;align-items:center;justify-content:center;padding:0 4px}
#wpn{position:absolute;right:58px;bottom:116px;width:108px;text-align:center;font-size:12px;font-weight:800;text-shadow:0 1px 2px #000;direction:rtl;line-height:1.3}
#wchips{display:flex;gap:3px;justify-content:center;margin-bottom:3px}
#wchips span{flex:1;font-size:10px;font-weight:700;padding:1px 0;border-radius:4px;background:rgba(0,0,0,.45);color:#c9ced6;border:1px solid transparent}
#wchips span.on{background:#d6332a;color:#fff;border-color:#fff}
#wpn small{display:block;font-weight:400;color:#c9ced6;font-size:11px}
#spd{position:absolute;right:64px;bottom:200px;font-size:12px;font-weight:700;text-shadow:0 1px 2px #000;direction:rtl}
#hpWrap{position:absolute;left:16px;top:8px;width:180px;direction:rtl;font-size:11px;font-weight:700;text-shadow:0 1px 2px #000;background:rgba(0,0,0,.28);border-radius:8px;padding:4px 8px;display:flex;flex-direction:column;gap:3px}
.sr{display:flex;align-items:center;gap:6px}
.sr span{width:38px;flex:none}
#hpWrap .bar{height:7px;flex:1}
#hpWrap .bar>i{background:#3aa7e0}
#hpWrap .bar>i#fuelBar{background:#e8c547}
#baseInd{position:absolute;right:16px;top:112px;width:96px;text-align:center;font-size:11px;font-weight:700;text-shadow:0 1px 2px #000;direction:rtl}
#baseInd svg,#tgtInd svg{width:26px;height:26px;display:block;margin:0 auto}
#tgtInd{position:absolute;right:116px;top:112px;width:96px;text-align:center;font-size:11px;font-weight:700;text-shadow:0 1px 2px #000;direction:rtl;color:#ffb35a;display:none}
.lk.gnd{border-color:#ff9a3a}
#hint{position:absolute;left:50%;top:46%;transform:translateX(-50%);font-size:15px;font-weight:800;background:rgba(10,14,20,.7);padding:4px 14px;border-radius:8px;direction:rtl;white-space:nowrap;display:none}
#svcUI{position:fixed;left:50%;top:14px;transform:translateX(-50%);width:400px;max-width:94vw;background:rgba(20,24,31,.92);border:1px solid rgba(90,176,255,.6);border-radius:14px;padding:10px 16px;direction:rtl;text-align:center;pointer-events:auto}
#svcUI h3{font-size:17px;color:#5ab0ff}
.sv{display:flex;align-items:center;gap:8px;font-size:12px;margin-top:6px}
.sv span{width:58px;flex:none;text-align:right}
.sv .bar{flex:1;height:9px}
.sv .bar>i{transition:none}
.scr{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(8,11,16,.78);direction:rtl;padding:10px}
.card{background:rgba(20,24,31,.95);border:1px solid rgba(255,181,71,.5);border-radius:16px;padding:16px 22px;max-width:96vw;width:460px;text-align:center;max-height:94vh;overflow-y:auto;touch-action:pan-y}
.card h1{font-size:30px;color:#ffb547;line-height:1.2}
.card h2{font-size:15px;color:#ffb547;margin:10px 0 6px;text-align:right}
.card p{font-size:14px;color:#c9ced6;margin:6px 0 12px;line-height:1.6}
.go{display:inline-block;border:0;border-radius:10px;background:#ffb547;color:#1a1d22;font:800 17px 'Cairo',sans-serif;padding:8px 30px;min-height:44px;cursor:pointer}
.go:disabled{background:#5a5f68;color:#9aa0a8}
.ghost{display:inline-block;border:1px solid #ffb547;border-radius:10px;background:transparent;color:#ffb547;font:700 15px 'Cairo',sans-serif;padding:8px 20px;min-height:44px;cursor:pointer}
.row{display:flex;gap:10px;justify-content:center;flex-wrap:wrap;margin-top:10px}
input#nameIn{width:100%;font:700 17px 'Cairo',sans-serif;padding:10px 12px;border-radius:10px;border:1px solid #5a5f68;background:#0f1217;color:#fff;text-align:right;user-select:text;-webkit-user-select:text}
input#nameIn:focus{outline:2px solid #ffb547}
label.lb{display:block;text-align:right;font-size:13px;color:#c9ced6;margin-bottom:4px}
.keys{font-size:12px;color:#9aa3ad;margin-top:10px}
#selCard{width:760px}
#flags{display:grid;grid-template-columns:repeat(7,minmax(0,1fr));gap:6px}
.fl{border:2px solid transparent;border-radius:8px;background:#0f1217;padding:5px 2px;color:#e8e8e8;font:700 11px 'Cairo',sans-serif;cursor:pointer;min-height:44px}
.fl svg{width:100%;max-width:46px;height:auto;display:block;margin:0 auto 2px;border-radius:2px}
.fl.on{border-color:#ffb547;background:#2a2418}
#types{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px}
.ty{border:2px solid transparent;border-radius:10px;background:#0f1217;padding:8px;text-align:right;color:#e8e8e8;cursor:pointer;font-family:'Cairo',sans-serif}
.ty.on{border-color:#ffb547;background:#2a2418}
.ty b{font-size:16px;color:#ffb547}
.ty small{display:block;font-size:11px;color:#aab0b8;margin-bottom:4px;line-height:1.4}
.st{display:flex;align-items:center;gap:6px;font-size:11px}
.st span{width:44px;flex:none}
.st .bar{flex:1;height:6px}
#ctrl{display:flex;gap:8px}
#ctrl button{flex:1}
.seg{border:1px solid #5a5f68;border-radius:10px;background:#0f1217;color:#e8e8e8;font:700 14px 'Cairo',sans-serif;padding:6px;min-height:44px;cursor:pointer}
.seg.on{border-color:#ffb547;background:#2a2418;color:#ffb547}
.seg small{display:block;font-weight:400;font-size:11px;color:#aab0b8}
#hangarUI{position:fixed;left:50%;bottom:14px;transform:translateX(-50%);width:440px;max-width:94vw;background:rgba(20,24,31,.92);border:1px solid rgba(255,181,71,.5);border-radius:14px;padding:12px 18px;direction:rtl;text-align:center}
#hangarUI h3{font-size:18px;color:#ffb547}
#hangarUI .bar{height:12px;margin:8px 0 4px}
#hangarUI .bar>i{background:#3fbf5f;transition:none}
#fuelTxt{font-size:13px;color:#c9ced6}
#hgTop{position:fixed;right:16px;top:12px;direction:rtl;font-weight:800;font-size:18px;color:#ffe08a;text-shadow:0 1px 3px #000}
#rot{display:none}
@media (orientation:portrait){#rot{display:flex;z-index:9}}
@media (max-height:430px){#flags .fl{font-size:10px}.card h1{font-size:24px}#selCard{padding:10px 16px}}
#flash{position:fixed;inset:0;background:#ff2a1a;opacity:0;pointer-events:none;transition:opacity .25s}
/* ===== قوائم بستايل اللعبة الأصلية: رمادي ضبابي وإطارات برتقالية ===== */
.mscr{position:fixed;inset:0;direction:rtl;background:linear-gradient(180deg,rgba(70,74,80,.5),rgba(28,30,34,.62));color:#f0f0f0;font-family:'Cairo',sans-serif}
.mscr::before,.mscr::after{content:'';position:absolute;top:14px;bottom:14px;width:10px;border:1px solid rgba(214,128,40,.55);pointer-events:none}
.mscr::before{left:8px;border-right:0}.mscr::after{right:8px;border-left:0}
.mscr.dim{background:rgba(10,12,16,.55);display:flex;align-items:center;justify-content:center}
.mscr.dim::before,.mscr.dim::after{display:none}
.mhead{position:absolute;top:8px;left:0;right:0;display:flex;align-items:center;justify-content:center;gap:14px;font-size:20px;font-weight:700;color:#f2f2f2;text-shadow:0 1px 3px #000}
.mhead::before,.mhead::after{content:'';width:120px;height:1px;background:linear-gradient(90deg,transparent,#d68028)}
.mhead::after{transform:scaleX(-1)}
.mgrid{position:absolute;top:52px;left:50%;transform:translateX(-50%);width:min(640px,74vw);display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:12px}
.mc{border:1px solid #c47a2c;background:rgba(60,62,66,.78);padding:6px 6px 0;cursor:pointer;font-family:'Cairo',sans-serif;color:#f2f2f2;display:flex;flex-direction:column;gap:5px;min-height:44px}
.mc .th{position:relative;height:clamp(48px,13vh,74px);border:1px solid rgba(0,0,0,.6)}
.mc .th svg{position:absolute;left:50%;top:50%;width:26px;height:26px;margin:-13px 0 0 -13px}
.mc .lb2{border-top:1px solid #c47a2c;margin:0 -6px;padding:3px 0;font-size:13px;font-weight:700;background:rgba(40,42,46,.9);text-align:center}
.mc.done .lb2{color:#9fe0a0}
.mc.next{border-color:#ffb547;box-shadow:0 0 12px rgba(255,181,71,.7)}
.mc .tag{position:absolute;bottom:3px;left:0;right:0;font-size:11px;font-weight:800;text-shadow:0 1px 2px #000}
.mc.locked .th{filter:grayscale(.7) brightness(.7)}
.arr,.back{direction:ltr}
.arr{position:absolute;top:50%;margin-top:-24px;width:44px;height:48px;border:0;background:none;color:#e08a2e;font-size:44px;line-height:40px;cursor:pointer}
.arrL{left:calc(50% - min(320px,37vw) - 54px)}.arrR{right:calc(50% - min(320px,37vw) - 54px)}
.side{position:absolute;width:52px;height:46px;border:0;background:#e88a2e;color:#fff;display:flex;align-items:center;justify-content:center;cursor:pointer;box-shadow:0 2px 6px rgba(0,0,0,.5)}
.side svg{width:24px;height:24px}
.sP{left:18px;bottom:72px;border-radius:0 24px 24px 0}.sS{left:18px;bottom:20px;border-radius:0 24px 24px 0}.sT{right:18px;bottom:20px;border-radius:24px 0 0 24px}
.mcoins{position:absolute;top:10px;right:30px;font-weight:800;font-size:16px;color:#ffe08a;text-shadow:0 1px 3px #000;direction:ltr}
.back{position:absolute;top:4px;left:24px;width:44px;height:44px;border:0;background:none;color:#e08a2e;font-size:40px;line-height:40px;cursor:pointer}
.pop{position:relative;width:min(520px,88vw);background:#f2efe9;color:#2a2a2a;border:2px solid #e08a2e;padding:22px 26px 16px;text-align:center;box-shadow:0 8px 30px rgba(0,0,0,.6)}
.pop p{font-size:15px;line-height:1.8;margin-bottom:14px}
.pop h2{font-size:22px;color:#c06a14;margin-bottom:6px}
.pop .x{position:absolute;top:-16px;left:-16px;width:34px;height:34px;border:2px solid #e08a2e;background:#fff;color:#e08a2e;font-size:18px;font-weight:800;cursor:pointer}
.ob{border:0;background:linear-gradient(180deg,#f6a24a,#dd7a1e);color:#3a1d00;font:800 16px 'Cairo',sans-serif;padding:7px 40px;min-height:44px;cursor:pointer;box-shadow:0 2px 0 #9a4c08}
.ob:disabled{background:#8a8a8a;color:#ddd;box-shadow:none}
.og{border:1px solid #c47a2c;background:rgba(40,42,46,.85);color:#f2f2f2;font:700 14px 'Cairo',sans-serif;padding:6px 16px;min-height:44px;cursor:pointer}
.pcard{position:absolute;top:50px;left:5vw;width:min(380px,44vw);bottom:18px;border:1px solid #c47a2c;background:rgba(55,57,61,.82);padding:14px;display:grid;grid-template-columns:minmax(0,1fr) minmax(0,1fr);gap:10px;align-content:start}
.pav{position:relative;aspect-ratio:1/1;border:1px solid #888;max-height:30vh}
.pav svg{width:100%;height:100%;display:block}
.ed{position:absolute;width:28px;height:28px;border:1px solid #fff;background:#111;color:#fff;font-size:14px;cursor:pointer;display:flex;align-items:center;justify-content:center}
.pflag{position:relative;display:flex;flex-direction:column;align-items:flex-start;gap:6px}
.pflag svg.f{width:70px;height:46px;border:1px solid #444}
.rnd{width:34px;height:34px}
.pname{grid-column:1/-1;display:flex;gap:8px;align-items:center}
.pname input{flex:1;min-width:0;font:800 15px 'Cairo',sans-serif;padding:6px 10px;border:0;background:linear-gradient(180deg,#e9953f,#c96f16);color:#fff;text-align:center;user-select:text;-webkit-user-select:text}
.pinfo{display:flex;flex-direction:column;gap:6px;font-size:14px}
.pinfo b{color:#ffe08a}

.avpick{position:absolute;top:50px;left:5vw;width:min(380px,44vw);background:#f2efe9;border:2px solid #e08a2e;padding:12px;display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px}
.avpick button{border:3px solid transparent;padding:0;background:none;cursor:pointer;aspect-ratio:1/1}
.avpick button.on{border-color:#e08a2e}
.avpick svg{width:100%;height:100%;display:block}
.fgrid{position:absolute;top:46px;left:50%;transform:translateX(-50%);width:min(700px,80vw);display:grid;grid-template-columns:repeat(6,minmax(0,1fr));gap:8px}
.fc{position:relative;border:2px solid transparent;background:rgba(60,62,66,.85);padding:4px 4px 0;cursor:pointer;font-family:'Cairo',sans-serif;color:#f2f2f2;min-height:44px}
.fc svg.pen{width:100%;height:auto;display:block;filter:drop-shadow(0 2px 2px rgba(0,0,0,.5))}
.fc .nm{font-size:12px;font-weight:700;padding:2px 0 4px;text-align:center;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.chk{position:absolute;top:-8px;left:-8px;width:24px;height:24px;border-radius:50%;background:#3fbf5f;color:#fff;font-size:15px;font-weight:800;display:flex;align-items:center;justify-content:center;box-shadow:0 1px 4px #000}
.fsel{position:absolute;bottom:62px;left:0;right:0;text-align:center;font-size:15px;font-weight:800;color:#ffe08a;text-shadow:0 1px 3px #000}
.fc svg.f{width:100%;height:auto;display:block;aspect-ratio:3/2}
.fc .lb2{display:flex;align-items:center;justify-content:center;gap:6px;border-top:1px solid #c47a2c;margin:6px -6px 0;padding:3px 0;font-size:13px;font-weight:700;background:rgba(40,42,46,.9)}
.fc .lb2 svg{width:18px;height:18px}
.fc.on{border-color:#ffb547;background:rgba(120,86,30,.9);box-shadow:0 0 14px rgba(255,181,71,.7)}
.ujoin{position:absolute;bottom:12px;left:50%;transform:translateX(-50%)}
.utabs{position:absolute;top:56px;left:26px;display:flex;flex-direction:column;gap:2px}
.utabs button{width:60px;height:62px;border:0;background:rgba(30,32,36,.9);color:#fff;cursor:pointer;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:2px;font:700 11px 'Cairo',sans-serif}
.utabs button.on{background:#c96f16;box-shadow:inset 0 0 0 2px #ffb547}
.utabs svg{width:28px;height:28px}
.upanel{position:absolute;top:56px;left:90px;width:min(230px,30vw);background:rgba(90,92,96,.85);padding:12px;display:flex;flex-direction:column;gap:10px}
.urow{font-size:14px;font-weight:700}
.urow .ub{display:flex;align-items:center;gap:8px;margin-top:4px;background:rgba(30,32,36,.9);padding:4px 8px;direction:ltr;justify-content:space-between}
.urow button{border:0;background:#e88a2e;color:#2a1400;font:800 13px 'Cairo',sans-serif;padding:2px 10px;min-height:32px;cursor:pointer;direction:rtl}
.urow button:disabled{background:#777;color:#ccc}
.sk{position:absolute;bottom:10px;right:5vw;display:flex;gap:10px;direction:rtl}
.sk button{position:relative;width:min(130px,15vw);border:1px solid #c47a2c;background:rgba(60,62,66,.9);padding:4px 4px 0;color:#f2f2f2;font:700 13px 'Cairo',sans-serif;cursor:pointer}
.sk button.on{border-color:#ffb547;box-shadow:0 0 12px rgba(255,181,71,.8)}
.sk .st2{display:block;font-size:11px;font-weight:700;padding:1px 0 3px}
.sk .own{color:#9fe0a0}.sk .use{color:#ffe08a}.sk .buy{color:#ffb547}
.sk svg.j{width:100%;height:46px;display:block}
.sk .lb2{border-top:1px solid #c47a2c;margin:4px -4px 0;padding:2px 0;background:rgba(40,42,46,.9)}
.sk .lk2{position:absolute;top:4px;left:4px;width:20px;height:20px}
.skAct{position:absolute;top:70px;right:calc(5vw + 120px)}
.toast{position:fixed;left:50%;top:52px;transform:translateX(-50%);background:rgba(10,12,16,.9);border:1px solid #e08a2e;color:#fff;padding:6px 16px;font:700 13px 'Cairo',sans-serif;direction:rtl;display:none;z-index:20}
#splash{display:flex;flex-direction:column;align-items:center;justify-content:center;gap:10px;background:radial-gradient(ellipse at 50% 40%,rgba(120,124,128,.45),rgba(30,32,36,.85))}
.logo{display:flex;flex-direction:column;align-items:center;direction:ltr}
.logo .en{font-family:'Teko',sans-serif;font-weight:700;font-size:clamp(54px,12vw,96px);line-height:.9;color:#f4f4f4;letter-spacing:2px;text-shadow:0 3px 10px rgba(0,0,0,.6)}
.logo .en b{font-size:1.25em;color:#fff}
.logo .ar{margin-top:-6px;font-size:clamp(20px,4vw,30px);font-weight:800;color:#e8892c;border-top:1px solid #e8892c;padding:0 30px;direction:rtl}
.spin{width:30px;height:30px;border:3px solid rgba(232,137,44,.25);border-top-color:#e8892c;border-radius:50%;animation:sp 1s linear infinite}
@keyframes sp{to{transform:rotate(360deg)}}
@media (prefers-reduced-motion:reduce){.spin{animation:none}}
.copy{position:absolute;bottom:10px;left:24px;right:24px;font-size:10px;color:rgba(255,255,255,.75);direction:rtl}
</style>
</head>
<body>
<canvas id="gl"></canvas>
<div id="flash"></div>

<div id="hud" style="display:none">
  <div id="tl"><div id="feed"></div></div>
  <div id="top">
    <span id="coins">0</span>
    <span class="pill" id="kills">قتلى 0</span>
    <span class="pill" id="wave">الموجة 1</span>
    <button id="bPause" class="tap" aria-label="إيقاف مؤقت">II</button>
  </div>
  <canvas id="radar" width="96" height="96"></canvas>
  <svg id="cross" viewBox="0 0 40 40" fill="none" stroke="#7dff9a" stroke-width="2" aria-hidden="true"><circle cx="20" cy="20" r="11"/><path d="M20 2v9M20 29v9M2 20h9M29 20h9"/></svg>
  <div id="locks"></div>
  <div id="warn">⚠ صاروخ قادم! اضغط فلير</div>
  <div id="big"></div>
  <div id="hpWrap"><div class="sr"><span>الدرع</span><div class="bar"><i id="hp"></i></div></div><div class="sr"><span>الوقود</span><div class="bar"><i id="fuelBar"></i></div></div></div>
  <div id="baseInd"><svg viewBox="0 0 24 24" fill="#5ab0ff" id="baseArrow" aria-hidden="true"><path d="M12 2l7 18-7-4-7 4z"/></svg><span id="baseTxt">القاعدة</span></div>
  <div id="tgtInd"><svg viewBox="0 0 24 24" fill="#ff9a3a" id="tgtArrow" aria-hidden="true"><path d="M12 2l7 18-7-4-7 4z"/></svg><span id="tgtTxt">الهدف</span></div>
  <div id="hint"></div>
  <div id="svcUI" style="display:none">
    <h3 id="svcT">صيانة في القاعدة</h3>
    <div class="sv"><span>الإصلاح</span><div class="bar"><i id="svHp" style="background:#3aa7e0"></i></div></div>
    <div class="sv"><span>الوقود</span><div class="bar"><i id="svFuel" style="background:#e8c547"></i></div></div>
    <div class="sv"><span>الذخيرة</span><div class="bar"><i id="svAmmo" style="background:#ff8a3d"></i></div></div>
    <div class="row"><button class="go tap" id="bSvcGo" disabled>إقلاع</button></div>
  </div>
  <div id="joy" class="tap" aria-label="الجويستك"><div id="knob"></div></div>
  <div id="spd">السرعة 0</div>
  <div id="thr" class="tap" aria-label="السرعة"><div id="thrFill"></div><div id="thrKnob"></div></div>
  <div id="wpn"><div id="wchips"><span data-w="0">رشاش</span><span data-w="1">عادي</span><span data-w="2">حراري</span></div><small id="refill">تجديد الذخيرة 30ث</small></div>
  <button id="bFire" class="btn tap" aria-label="إطلاق"><svg id="icGun" viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2"><circle cx="12" cy="12" r="8"/><path d="M12 2v5M12 17v5M2 12h5M17 12h5"/></svg><svg id="icMsl" viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="display:none"><path d="M12 2c2 2 3 5 3 9v6H9v-6c0-4 1-7 3-9z"/><path d="M9 14l-3 3v3l3-2M15 14l3 3v3l-3-2"/></svg><span class="cnt" id="ammoN">300</span></button>
  <button id="bSw" class="btn tap" aria-label="تبديل السلاح"><svg viewBox="0 0 24 24" fill="none" stroke="#ffb547" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 12a8 8 0 0 1 14-5.3M20 12a8 8 0 0 1-14 5.3"/><path d="M18 2v5h-5M6 22v-5h5"/></svg></button>
  <button id="bFlr" class="btn tap" aria-label="فلير"><svg viewBox="0 0 24 24" fill="none" stroke="#ffb547" stroke-width="2" stroke-linecap="round"><path d="M12 3v6M12 15v6M3 12h6M15 12h6M6 6l3 3M15 15l3 3M18 6l-3 3M9 15l-3 3"/></svg><span class="cnt" id="flrN">12</span></button>
  <button id="bCam" class="btn tap" aria-label="الكاميرا"><svg viewBox="0 0 24 24" fill="none" stroke="#ffb547" stroke-width="2"><path d="M2 12s4-7 10-7 10 7 10 7-4 7-10 7S2 12 2 12z"/><circle cx="12" cy="12" r="3"/></svg></button>
</div>

<!-- شاشة البداية -->
<div id="splash" class="mscr">
  <div class="logo"><div class="en">SKY <b>F</b>ALCONS</div><div class="ar">صقور السماء</div></div>
  <div class="spin" id="spin" aria-hidden="true"></div>
  <button class="ob" id="bGo" style="display:none">إنطلق</button>
  <div class="keys" id="loadTxt">جاري تحميل مجسم الطائرة...</div>
  <button class="og" id="bFull">⛶ شاشة كاملة</button>
  <div class="copy">نسخة تجريبية مستوحاة من «صقور السماء» · مجسمات من Sketchfab: F-16 Fighting Falcon (bohmerang، CC BY-NC-SA 4.0) · Iranian Sevom Khordad SAM (42manako، CC BY 4.0) · Desert Landscape (Šimon Ustal، CC BY 4.0) · RUNWAY (pranav27، CC BY 4.0)</div>
</div>

<!-- المهمات -->
<div id="menu" class="mscr" style="display:none">
  <div class="mhead">المهمات</div>
  <div class="mcoins" id="mCoins">0</div>
  <div class="mgrid" id="mgrid"></div>
  <button class="arr arrL" id="mNext" aria-label="الصفحة التالية">‹</button>
  <button class="arr arrR" id="mPrev" aria-label="الصفحة السابقة">›</button>
  <button class="side sP" id="bProfile" aria-label="الملف"><svg viewBox="0 0 24 24" fill="#fff"><circle cx="12" cy="8" r="4.5"/><path d="M3 21c0-5 4-8 9-8s9 3 9 8z"/></svg></button>
  <button class="side sS" id="bShare" aria-label="مشاركة"><svg viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2.4"><circle cx="18" cy="5" r="2.6" fill="#fff"/><circle cx="6" cy="12" r="2.6" fill="#fff"/><circle cx="18" cy="19" r="2.6" fill="#fff"/><path d="M8 11l8-5M8 13l8 5"/></svg></button>
  <button class="side sT" id="bTools" aria-label="قاعدة التطوير"><svg viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2.2" stroke-linecap="round"><path d="M14 6a4 4 0 0 0 5 5l-9 9-3-3 9-9a4 4 0 0 0-2-2z"/><path d="M4 4l6 6M3 7l4-4"/></svg></button>
</div>

<!-- وصف المهمة -->
<div id="brief" class="mscr dim" style="display:none"><div class="pop">
  <button class="x" id="bBriefX" aria-label="إغلاق">✕</button>
  <h2 id="briefT"></h2>
  <p id="briefTxt"></p>
  <button class="ob" id="bBriefGo">إبدأ المهمة</button>
</div></div>

<!-- نجاح المهمة -->
<div id="done" class="mscr dim" style="display:none"><div class="pop">
  <h2 id="doneT">نجحت المهمة!</h2>
  <p id="doneP"></p>
  <button class="ob" id="bDone">المهمات</button>
</div></div>

<!-- الملف -->
<div id="profile" class="mscr" style="display:none">
  <div class="mhead">الملف</div>
  <button class="back" id="pBack" aria-label="رجوع">‹</button>
  <div class="pcard">
    <div class="pav"><div id="avatar"></div><button class="ed" id="edAv" style="top:-10px;right:-10px" aria-label="تغيير الصورة">✎</button></div>
    <div class="pflag"><svg class="f" id="pFlag" viewBox="0 0 30 20" aria-hidden="true"></svg><div id="pRound" class="rnd"></div><div id="pCountry" style="font-weight:700"></div><button class="ed" id="edFlag" style="top:0;left:0" aria-label="تغيير سلاح الجو">✎</button></div>
    <div class="pname"><label for="nameIn" style="font-size:13px;white-space:nowrap">الاسم</label><input id="nameIn" maxlength="16" autocomplete="off" placeholder="اكتب اسمك"></div>
    <div class="pinfo"><div>العملات: <b id="pCoins">0</b></div><div>الرتبة: <b id="pRank"></b></div></div>
    <div style="display:flex;align-items:end;justify-content:flex-end"><button class="og" id="bLang">عربي ‹</button></div>
  </div>
  <div id="avPick" class="avpick" style="display:none"></div>
</div>

<!-- القوات الجوية -->
<div id="forces" class="mscr" style="display:none">
  <div class="mhead">القوات الجوية</div>
  <button class="back" id="fBack" aria-label="رجوع">‹</button>
  <div class="fgrid" id="fgrid"></div>
  <button class="arr arrL" id="fNext" aria-label="الصفحة التالية">‹</button>
  <button class="arr arrR" id="fPrev" aria-label="الصفحة السابقة">›</button>
  <div class="fsel" id="fSel"></div>
  <button class="ob ujoin" id="bJoin">انضم</button>
</div>

<!-- قاعدة التطوير -->
<div id="upgrade" class="mscr" style="display:none">
  <div class="mhead">قاعدة التطوير</div>
  <button class="back" id="uBack" aria-label="رجوع">‹</button>
  <div class="mcoins" id="uCoins">0</div>
  <div class="utabs">
    <button id="tBody" class="on"><svg viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2"><circle cx="12" cy="12" r="3.5"/><path d="M12 2v3M12 19v3M2 12h3M19 12h3M5 5l2 2M17 17l2 2M19 5l-2 2M7 17l-2 2"/></svg>الهيكل</button>
    <button id="tWpn"><svg viewBox="0 0 24 24" fill="#fff"><path d="M3 13l11-5 7 2-7 2-11 3z"/><path d="M6 14l-3 4h3l3-3z"/></svg>الأسلحة</button>
  </div>
  <div class="upanel" id="upanel"></div>
  <button class="ob skAct" id="bSkin">اختيرت</button>
  <div class="sk" id="skins"></div>
</div>
<div class="toast" id="toast"></div>

<!-- 3) الحظيرة وتزويد الوقود -->
<div id="hgTop" style="display:none"></div>
<div id="hangarUI" style="display:none">
  <h3 id="hgName">صقر — العراق</h3>
  <div class="bar"><i id="fuel" style="width:0%"></i></div>
  <div id="fuelTxt">جاري تزويد الوقود... 0%</div>
  <div class="row">
    <button class="ghost" id="bBack">رجوع للمهمات</button>
    <button class="go" id="bTakeoff" disabled>ابدأ</button>
  </div>
</div>

<div id="over" class="scr" style="display:none"><div class="card">
  <h1 id="overT">سقطت طائرتك</h1>
  <p id="overP"></p>
  <div class="row">
    <button class="go" id="bAgain">طيران جديد</button>
    <button class="ghost" id="bChange">تغيير الطائرة</button>
  </div>
</div></div>
<div id="paused" class="scr" style="display:none"><div class="card">
  <h1>إيقاف مؤقت</h1><p>اللعبة متوقفة.</p>
  <div class="row"><button class="go" id="bResume">كمّل</button><button class="ghost" id="bQuit">رجوع للمهمات</button><button class="ghost" id="bFull2">⛶ شاشة كاملة</button></div>
</div></div>
<div id="rot" class="scr"><div class="card"><h1>أدر الهاتف</h1><p>اللعبة تشتغل بالوضع الأفقي.</p></div></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/loaders/GLTFLoader.js"></script>
<script>
{
const $ = id => document.getElementById(id);
const rand = (a, b) => a + Math.random() * (b - a);
const clamp = (v, a, b) => Math.max(a, Math.min(b, v));
const wrapA = a => { while (a > Math.PI) a -= 2 * Math.PI; while (a < -Math.PI) a += 2 * Math.PI; return a; };
const V = () => new THREE.Vector3();
const store = { get(k, d) { try { const v = localStorage.getItem(k); return v === null ? d : v; } catch (e) { return d; } }, set(k, v) { try { localStorage.setItem(k, v); } catch (e) {} } };

/* ---------- الدول والطائرات ---------- */
const star = (cx, cy, r, fill, stroke) => {
  let p = '';
  for (let i = 0; i < 10; i++) { const a = -Math.PI / 2 + i * Math.PI / 5, rr = i % 2 ? r * 0.42 : r; p += (i ? 'L' : 'M') + (cx + Math.cos(a) * rr).toFixed(2) + ' ' + (cy + Math.sin(a) * rr).toFixed(2); }
  return stroke ? `<path d="${p}Z" fill="none" stroke="${stroke}" stroke-width="0.9"/>` : `<path d="${p}Z" fill="${fill}"/>`;
};
const H3 = (a, b, c) => `<rect width="30" height="6.67" fill="${a}"/><rect y="6.67" width="30" height="6.67" fill="${b}"/><rect y="13.33" width="30" height="6.67" fill="${c}"/>`;
const starN = (cx, cy, r, n, fill, inner = 0.42) => {
  let p = '';
  for (let i = 0; i < n * 2; i++) { const a = -Math.PI / 2 + i * Math.PI / n, rr = i % 2 ? r * inner : r; p += (i ? 'L' : 'M') + (cx + Math.cos(a) * rr).toFixed(2) + ' ' + (cy + Math.sin(a) * rr).toFixed(2); }
  return `<path d="${p}Z" fill="${fill}"/>`;
};
const pentagram = (cx, cy, r, col) => {
  const P = k => { const a = -Math.PI / 2 + k * 2 * Math.PI / 5; return (cx + Math.cos(a) * r).toFixed(2) + ' ' + (cy + Math.sin(a) * r).toFixed(2); };
  return `<path d="M${P(0)}L${P(2)}L${P(4)}L${P(1)}L${P(3)}Z" fill="none" stroke="${col}" stroke-width="0.8" stroke-linejoin="round"/>`;
};
// هلال: دائرة خارجية ناقص دائرة داخلية مزاحة باتجاه (ux,uy)
const crescent = (cx, cy, R, r, off, ux, uy, fill) => {
  const a = (R * R - r * r + off * off) / (2 * off), h = Math.sqrt(Math.max(0, R * R - a * a)), px = -uy, py = ux;
  const bx = cx + a * ux, by = cy + a * uy, f = v => v.toFixed(2);
  const p1 = [bx + h * px, by + h * py], p2 = [bx - h * px, by - h * py];
  return `<path d="M${f(p2[0])} ${f(p2[1])}A${R} ${R} 0 1 0 ${f(p1[0])} ${f(p1[1])}A${r} ${r} 0 1 1 ${f(p2[0])} ${f(p2[1])}Z" fill="${fill}"/>`;
};
const serrated = (x0, x1, n, col) => { // شريط أبيض مسنّن (قطر والبحرين)
  let p = `M0 0H${x0}`; const h = 20 / n;
  for (let i = 0; i < n; i++) p += `L${x1} ${(i + 0.5) * h}L${x0} ${(i + 1) * h}`;
  return `<path d="${p}H0Z" fill="${col}"/>`;
};
const COUNTRIES = [
  { name: 'العراق', colors: [0xce1126, 0xffffff, 0x111111], svg: H3('#ce1126', '#fff', '#111') + '<text x="15" y="11.5" text-anchor="middle" font-size="3.6" font-weight="700" font-family="Cairo,sans-serif" fill="#007a3d">الله أكبر</text>' },
  { name: 'السعودية', colors: [0x006c35, 0xffffff, 0x006c35], svg: '<rect width="30" height="20" fill="#006c35"/><text x="15" y="9" text-anchor="middle" font-size="2.7" font-weight="700" font-family="Cairo,sans-serif" fill="#fff" textLength="19" lengthAdjust="spacingAndGlyphs">لا إله إلا الله محمد رسول الله</text><path d="M8 12.6H21.4L22.6 12.1L21.4 11.8H8Z" fill="#fff"/><rect x="7.4" y="11.4" width=".6" height="2" fill="#fff"/><rect x="6.6" y="12.2" width="1" height=".5" fill="#fff"/>' },
  { name: 'مصر', colors: [0xce1126, 0xffffff, 0x111111], svg: H3('#ce1126', '#fff', '#111') + '<path d="M15 7.3c-.5 0-.8.4-.8.8l-2.4-.4.9 1.1-1.1.3 1.3.7-.8.6 1.5.3.5 1.6h1.8l.5-1.6 1.5-.3-.8-.6 1.3-.7-1.1-.3.9-1.1-2.4.4c0-.4-.3-.8-.8-.8z" fill="#c09300"/><rect x="14.2" y="11.6" width="1.6" height="1.3" fill="#c09300"/>' },
  { name: 'الأردن', colors: [0x111111, 0xffffff, 0x007a3d], svg: H3('#111', '#fff', '#007a3d') + '<path d="M0 0L15 10L0 20Z" fill="#ce1126"/>' + starN(5, 10, 1.6, 7, '#fff', 0.5) },
  { name: 'المغرب', colors: [0xc1272d, 0x006233, 0xc1272d], svg: '<rect width="30" height="20" fill="#c1272d"/>' + pentagram(15, 10.4, 4.4, '#006233') },
  { name: 'الجزائر', colors: [0x006233, 0xffffff, 0xd21034], svg: '<rect width="15" height="20" fill="#006233"/><rect x="15" width="15" height="20" fill="#fff"/>' + crescent(15, 10, 5, 4, 1.3, 1, 0, '#d21034') + starN(17.6, 10, 1.9, 5, '#d21034') },
  { name: 'الإمارات', colors: [0xce1126, 0x00732f, 0x111111], svg: '<rect width="30" height="20" fill="#fff"/><rect x="7.5" width="22.5" height="6.67" fill="#00732f"/><rect x="7.5" y="13.33" width="22.5" height="6.67" fill="#111"/><rect width="7.5" height="20" fill="#ce1126"/>' },
  { name: 'سوريا', colors: [0xce1126, 0xffffff, 0x111111], svg: H3('#ce1126', '#fff', '#111') + starN(10, 10, 2, 5, '#007a3d') + starN(20, 10, 2, 5, '#007a3d') },
  { name: 'الكويت', colors: [0x007a3d, 0xffffff, 0xce1126], svg: H3('#007a3d', '#fff', '#ce1126') + '<path d="M0 0L7.5 6.67V13.33L0 20Z" fill="#111"/>' },
  { name: 'قطر', colors: [0x8a1538, 0xffffff, 0x8a1538], svg: '<rect width="30" height="20" fill="#8a1538"/>' + serrated(7.4, 10.6, 9, '#fff') },
  { name: 'البحرين', colors: [0xce1126, 0xffffff, 0xce1126], svg: '<rect width="30" height="20" fill="#ce1126"/>' + serrated(7.5, 11, 5, '#fff') },
  { name: 'عُمان', colors: [0xdb161b, 0xffffff, 0x008000], svg: '<rect width="30" height="6.67" fill="#fff"/><rect y="6.67" width="30" height="6.67" fill="#db161b"/><rect y="13.33" width="30" height="6.67" fill="#008000"/><rect width="8" height="20" fill="#db161b"/><path d="M2.2 2.2L5.8 5.8M5.8 2.2L2.2 5.8M4 1.6V6.4" stroke="#fff" stroke-width=".55"/>' },
  { name: 'اليمن', colors: [0xce1126, 0xffffff, 0x111111], svg: H3('#ce1126', '#fff', '#111') },
  { name: 'فلسطين', colors: [0x111111, 0xffffff, 0x007a3d], svg: H3('#111', '#fff', '#007a3d') + '<path d="M0 0L10 10L0 20Z" fill="#ce1126"/>' },
  { name: 'لبنان', colors: [0xed1c24, 0xffffff, 0x00a651], svg: '<rect width="30" height="20" fill="#ed1c24"/><rect y="5" width="30" height="10" fill="#fff"/><path d="M15 5.6L18.4 9.2H16.6L19.2 11.8H16.6L19.8 14.3H10.2L13.4 11.8H10.8L13.4 9.2H11.6Z" fill="#00a651"/><rect x="14.5" y="14.2" width="1" height="1.2" fill="#00a651"/>' },
  { name: 'السودان', colors: [0xd21034, 0xffffff, 0x007229], svg: H3('#d21034', '#fff', '#111') + '<path d="M0 0L10 10L0 20Z" fill="#007229"/>' },
  { name: 'ليبيا', colors: [0xe70013, 0x111111, 0x239e46], svg: '<rect width="30" height="5" fill="#e70013"/><rect y="5" width="30" height="10" fill="#111"/><rect y="15" width="30" height="5" fill="#239e46"/>' + crescent(14.2, 10, 2.6, 2.1, 0.75, 1, 0, '#fff') + starN(16.4, 10, 1.1, 5, '#fff') },
  { name: 'تونس', colors: [0xe70013, 0xffffff, 0xe70013], svg: '<rect width="30" height="20" fill="#e70013"/><circle cx="15" cy="10" r="5" fill="#fff"/>' + crescent(14.6, 10, 3.75, 3, 0.95, 1, 0, '#e70013') + starN(15.9, 10, 1.75, 5, '#e70013') },
  { name: 'موريتانيا', colors: [0x00a95c, 0xffd700, 0xd01c1f], svg: '<rect width="30" height="20" fill="#00a95c"/><rect width="30" height="3" fill="#d01c1f"/><rect y="17" width="30" height="3" fill="#d01c1f"/>' + crescent(15, 9.4, 4.4, 3.8, 1.2, 0, -1, '#ffd700') + starN(15, 7.6, 1.5, 5, '#ffd700') },
  { name: 'الصومال', colors: [0x4189dd, 0xffffff, 0x4189dd], svg: '<rect width="30" height="20" fill="#4189dd"/>' + starN(15, 10.3, 4, 5, '#fff', 0.4) },
  { name: 'جيبوتي', colors: [0x6ab2e7, 0x12ad2b, 0xffffff], svg: '<rect width="30" height="10" fill="#6ab2e7"/><rect y="10" width="30" height="10" fill="#12ad2b"/><path d="M0 0L13 10L0 20Z" fill="#fff"/>' + starN(4.6, 10, 1.7, 5, '#d7141a') },
  { name: 'جزر القمر', colors: [0xffc61e, 0x3a75c4, 0x3d8e33], svg: '<rect width="30" height="5" fill="#ffc61e"/><rect y="5" width="30" height="5" fill="#fff"/><rect y="10" width="30" height="5" fill="#ce1126"/><rect y="15" width="30" height="5" fill="#3a75c4"/><path d="M0 0L13 10L0 20Z" fill="#3d8e33"/>' + crescent(4.4, 10, 3.6, 3, 1.1, 1, 0, '#fff') + [6.8, 9, 11, 13.2].map(y => starN(6.4, y, 0.55, 5, '#fff')).join('') }
];
/* علم بشكل راية بإطار ذهبي، مثل الصور */
const pennant = c => `<svg viewBox="0 0 34 30" class="pen" aria-hidden="true"><path d="M2 2H32V22L17 28L2 22Z" fill="#c9a227"/><svg x="3.2" y="3.2" width="27.6" height="23" viewBox="0 0 30 25" preserveAspectRatio="none"><clipPath id="pc"><path d="M0 0H30V19L15 25L0 19Z"/></clipPath><g clip-path="url(#pc)"><svg width="30" height="25" viewBox="0 0 30 20" preserveAspectRatio="none">${c.svg}</svg></g></svg></svg>`;
const hex = n => '#' + n.toString(16).padStart(6, '0');
const roundel = c => `<svg viewBox="0 0 20 20" aria-hidden="true" style="width:100%;height:100%"><circle cx="10" cy="10" r="9.5" fill="${hex(c.colors[0])}" stroke="#222" stroke-width=".6"/><circle cx="10" cy="10" r="6.4" fill="${hex(c.colors[1])}"/><circle cx="10" cy="10" r="3.4" fill="${hex(c.colors[2])}"/></svg>`;
const JET = 'M50 8 L56 40 L92 60 L92 67 L56 59 L55 78 L67 87 L67 92 L50 87 L33 92 L33 87 L45 78 L44 59 L8 67 L8 60 L44 40 Z';
const AVATARS = [
  ['#1c2430', '#4a5a70', `<circle cx="50" cy="42" r="22" fill="#2b2f36"/><path d="M30 44h40v10a20 14 0 0 1-40 0z" fill="#d68028"/><path d="M22 96c4-22 18-30 28-30s24 8 28 30z" fill="#3a4250"/>`],
  ['#f2a24a', '#5a2d10', `<path d="${'M50 18 L54 44 L80 58 L80 63 L54 56 L53 72 L62 80 L62 84 L50 80 L38 84 L38 80 L47 72 L46 56 L20 63 L20 58 L46 44 Z'}" fill="#1a1a1a"/>`],
  ['#8a949e', '#3a4048', `<path d="${JET}" fill="#2a2f36" transform="rotate(-30 50 50) scale(.85) translate(9 9)"/>`],
  ['#ffcf7a', '#c45a1a', `<circle cx="50" cy="60" r="20" fill="#ffe7a8"/><path d="M0 72h100v28H0z" fill="#3a2410"/><path d="M50 30 L53 48 L72 56 L72 60 L53 55 L52 66 L58 70 L50 68 L42 70 L48 66 L47 55 L28 60 L28 56 L47 48 Z" fill="#1a1a1a"/>`],
  ['#20324a', '#0c121c', `<path d="M14 54 C30 30 44 34 50 46 C56 34 70 30 86 54 C70 46 60 50 50 62 C40 50 30 46 14 54Z" fill="#d68028"/><circle cx="50" cy="44" r="5" fill="#ffe08a"/>`],
  ['#6a7a5a', '#2e3a22', `<circle cx="50" cy="40" r="20" fill="#4a5a3a"/><rect x="32" y="38" width="36" height="10" rx="5" fill="#111"/><path d="M20 96c4-20 18-28 30-28s26 8 30 28z" fill="#3e4a30"/>`]
];
const avatarSvg = i => { const a = AVATARS[i]; return `<svg viewBox="0 0 100 100" aria-hidden="true"><defs><linearGradient id="ag${i}" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="${a[0]}"/><stop offset="1" stop-color="${a[1]}"/></linearGradient></defs><rect width="100" height="100" fill="url(#ag${i})"/>${a[2]}</svg>`; };
const RANKS = ['تلميذ مرشح', 'ملازم', 'ملازم أول', 'نقيب', 'رائد', 'مقدم', 'عقيد', 'عميد', 'لواء', 'فريق', 'فريق أول', 'مشير'];
const SKY_PRESETS = {
  day: { top: 0x3f6fae, mid: 0x8fb0d0, hor: 0xd9e0e4, fog: 0xd2dade, sun: 0xfff2dd, si: 0.9, thumb: 'linear-gradient(180deg,#6f9cd0,#cfdbe4 55%,#c9b48a 56%,#a8916a)' },
  sunset: { top: 0x2e3b5e, mid: 0xc98a5e, hor: 0xf2b77e, fog: 0xdcae86, sun: 0xffc488, si: 0.85, thumb: 'linear-gradient(180deg,#3a4668,#e09a64 50%,#f5c88e 56%,#9a7650)' },
  dusk: { top: 0x1c2340, mid: 0x4f5680, hor: 0xa58c94, fog: 0x837c8a, sun: 0xb8b4d8, si: 0.6, thumb: 'linear-gradient(180deg,#1c2340,#5a5f88 50%,#a08c94 56%,#5a4e44)' },
  sea: { top: 0x2f5e96, mid: 0x7fa6cc, hor: 0xc8d6e0, fog: 0xbfcfdb, sun: 0xfff2dd, si: 0.9, thumb: 'linear-gradient(180deg,#3a6aa0,#bcd0de 55%,#2e5a7a 56%,#1e3a52)' }
};
const MISSIONS = [
  { name: 'اللعب الحر', free: true, sky: 'day', brief: 'هذه مرحلة اللعب الحر والتدريب في اللعبة. لا يوجد هنا ترقيات أو عقوبات عسكرية.' },
  { n: 1, air: 2, skill: 0, sky: 'sunset', brief: 'طائرتان معاديتان اخترقتا أجواءنا قرب الحدود. اعترضهما ودمّرهما قبل أن تصلا للقاعدة.' },
  { n: 2, ground: 5, sky: 'day', brief: 'رتل دبابات ومدافع مضادة للطائرات يتقدم في الصحراء. اتبع السهم البرتقالي ودمّر كل الأهداف الأرضية.' },
  { n: 3, air: 3, skill: 1, sky: 'sea', brief: 'سرب معادٍ فوق الساحل. ثلاث طائرات بطيارين أمهر من قبل. لا تدعهم يلتفون خلفك.' },
  { n: 4, ground: 6, sam: true, escort: 1, sky: 'sunset', brief: 'العدو نصب منصة صواريخ أرض جو تحميها طائرة. استخدم الفلير ودمّر المنصة أولاً.' },
  { n: 5, air: 4, skill: 2, sky: 'dusk', brief: 'أربع طائرات معادية في هجوم مسائي. حافظ على سرعتك واستخدم الصواريخ الحرارية.' },
  { n: 6, air: 2, ground: 5, skill: 2, sky: 'day', brief: 'مهمة مشتركة: طائرتان تحميان قاعدة دبابات. نظّف السماء ثم اضرب الأرض.' },
  { n: 7, air: 5, skill: 3, sky: 'dusk', brief: 'خمس طائرات معادية تهاجم في وقت الغروب. هذه أصعب معركة جوية حتى الآن.' },
  { n: 8, ground: 8, sam: true, escort: 2, sky: 'sea', brief: 'قاعدة إمداد معادية محصنة بمنصات صواريخ ومدافع، وطائرتان للحماية.' },
  { n: 9, air: 6, skill: 4, sky: 'sunset', brief: 'نخبة طياري العدو. ست طائرات سريعة ودقيقة. ارجع للقاعدة للصيانة إذا احتجت.' },
  { n: 10, air: 3, ground: 7, sam: true, skill: 4, sky: 'dusk', brief: 'الهجوم الكبير: سماء مليئة بالطائرات وأرض مليئة بالدفاعات.' },
  { n: 11, air: 6, ground: 6, sam: true, skill: 6, sky: 'day', brief: 'المعركة الأخيرة. دمّر كل قوات العدو وارفع علم بلدك في السماء.' }
];
const SKINS = [{ name: 'الرسمية', price: 0, tint: null }, { name: 'العسكرية', price: 500, tint: 0x8c9a6c }, { name: 'الوطنية', price: 800, tint: 'flag' }];
const UPS = { armor: { name: 'الدرع', val: l => 100 + l * 15 }, cool: { name: 'التبريد', val: l => (0.1 + l * 0.05).toFixed(2) }, power: { name: 'القوة', val: l => 20 + l * 4 }, reload: { name: 'إعادة التعبئة', val: l => Math.max(16, 30 - l * 3) + 'ث' }, load: { name: 'الحمولة', val: l => 6 + l } };
const UP_MAX = 5, upCost = l => 150 + l * 60;
const TYPES = [
  { name: 'صقر', desc: 'متوازنة، مستوحاة من F-16', spd: 1, turn: 1, hp: 100, msl: 6, scale: 1, fins: 2 },
  { name: 'شاهين', desc: 'خفيفة وسريعة، درعها أقل', spd: 1.15, turn: 1.15, hp: 75, msl: 5, scale: 0.88, fins: 1 },
  { name: 'نسر', desc: 'ثقيلة ومدرّعة، صواريخ أكثر', spd: 0.88, turn: 0.85, hp: 150, msl: 8, scale: 1.15, fins: 2 }
];

/* ---------- المشهد ---------- */
const renderer = new THREE.WebGLRenderer({ canvas: $('gl'), antialias: true, powerPreference: 'high-performance' });
renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 1.25));
const scene = new THREE.Scene();
const SKY = 0x8d99a6;
scene.background = new THREE.Color(0xd2dade);
scene.fog = new THREE.Fog(0xd2dade, 700, 3400);
/* سماء متدرجة مع شمس، وتستخدم أيضاً لانعكاس المعدن على الطائرة */
function makeSkyDome(r) {
  const geo = new THREE.SphereGeometry(r, 32, 16), pos = geo.attributes.position, cols = [];
  const top = new THREE.Color(0x3f6fae), mid = new THREE.Color(0x8fb0d0), hor = new THREE.Color(0xd9e0e4), c = new THREE.Color();
  for (let i = 0; i < pos.count; i++) {
    const h = pos.getY(i) / r;
    if (h > 0.25) c.copy(mid).lerp(top, Math.min(1, (h - 0.25) / 0.6)); else if (h > 0) c.copy(hor).lerp(mid, h / 0.25); else c.copy(hor).lerp(new THREE.Color(0xb7a47e), Math.min(1, -h * 3));
    cols.push(c.r, c.g, c.b);
  }
  geo.setAttribute('color', new THREE.Float32BufferAttribute(cols, 3));
  return new THREE.Mesh(geo, new THREE.MeshBasicMaterial({ vertexColors: true, side: THREE.BackSide, fog: false, depthWrite: false }));
}
const sky = makeSkyDome(5000); sky.renderOrder = -1; scene.add(sky);
function setSky(name) {
  const p = SKY_PRESETS[name] || SKY_PRESETS.day, geo = sky.geometry, pos = geo.attributes.position, col = geo.attributes.color, r = 5000;
  const top = new THREE.Color(p.top), mid = new THREE.Color(p.mid), hor = new THREE.Color(p.hor), gnd = new THREE.Color(0xb7a47e), c = new THREE.Color();
  for (let i = 0; i < pos.count; i++) {
    const h = pos.getY(i) / r;
    if (h > 0.25) c.copy(mid).lerp(top, Math.min(1, (h - 0.25) / 0.6)); else if (h > 0) c.copy(hor).lerp(mid, h / 0.25); else c.copy(hor).lerp(gnd, Math.min(1, -h * 3));
    col.setXYZ(i, c.r, c.g, c.b);
  }
  col.needsUpdate = true;
  scene.fog.color.setHex(p.fog); scene.background.setHex(p.fog);
  sun.color.setHex(p.sun); sun.intensity = p.si;
  sunSpr.material.color.setHex(p.sun);
}
const softTex = (() => { const c = document.createElement('canvas'); c.width = c.height = 128; const x = c.getContext('2d'); const g = x.createRadialGradient(64, 64, 0, 64, 64, 64); g.addColorStop(0, 'rgba(255,255,255,1)'); g.addColorStop(0.45, 'rgba(255,255,255,.75)'); g.addColorStop(1, 'rgba(255,255,255,0)'); x.fillStyle = g; x.fillRect(0, 0, 128, 128); return new THREE.CanvasTexture(c); })();
const sunSpr = new THREE.Sprite(new THREE.SpriteMaterial({ map: softTex, color: 0xfff4d8, fog: false, depthWrite: false, blending: THREE.AdditiveBlending }));
sunSpr.scale.set(700, 700, 1); scene.add(sunSpr);
let envMap = null;
try {
  const pm = new THREE.PMREMGenerator(renderer), envScene = new THREE.Scene();
  envScene.add(makeSkyDome(100));
  const sunBall = new THREE.Mesh(new THREE.SphereGeometry(8, 8, 6), new THREE.MeshBasicMaterial({ color: 0xffffff })); sunBall.position.set(45, 70, 30); envScene.add(sunBall);
  envMap = pm.fromScene(envScene, 0.02).texture;
} catch (e) { envMap = null; }
const camera = new THREE.PerspectiveCamera(65, 1, 0.5, 6000);
scene.add(new THREE.HemisphereLight(0xdfe8f0, 0x8a7350, 0.95));
const sun = new THREE.DirectionalLight(0xfff2dd, 0.9);
sun.position.set(300, 600, 200);
scene.add(sun);
function resize() { renderer.setSize(innerWidth, innerHeight, false); camera.aspect = innerWidth / innerHeight; camera.updateProjectionMatrix(); }
addEventListener('resize', resize);
resize();

/* الأرض: كثبان تتكرر كل 800 وحدة */
const K = 2 * Math.PI / 800;
const groundH = (x, z) => 14 * Math.sin(K * x) * Math.cos(K * z) + 6 * Math.sin(3 * K * x + 2 * K * z) + 3 * Math.cos(5 * K * z);
const gGeo = new THREE.PlaneGeometry(8000, 8000, 100, 100);
gGeo.rotateX(-Math.PI / 2);
const gp = gGeo.attributes.position, gc = [];
const sandA = new THREE.Color(0xc9b48a), sandB = new THREE.Color(0x9c845c), tmpC = new THREE.Color();
for (let i = 0; i < gp.count; i++) {
  const h = groundH(gp.getX(i), gp.getZ(i));
  gp.setY(i, h);
  tmpC.copy(sandB).lerp(sandA, clamp((h + 20) / 40, 0, 1));
  gc.push(tmpC.r, tmpC.g, tmpC.b);
}
gGeo.setAttribute('color', new THREE.Float32BufferAttribute(gc, 3));
gGeo.computeVertexNormals();
function loadTex(urls, ok, i = 0) {
  if (!THREE.TextureLoader || i >= urls.length) return;
  new THREE.TextureLoader().load(urls[i], ok, undefined, () => loadTex(urls, ok, i + 1));
}
const sandTex = (() => {
  const c = document.createElement('canvas'); c.width = c.height = 256; const x = c.getContext('2d');
  x.fillStyle = '#d9c49a'; x.fillRect(0, 0, 256, 256);
  const img = x.getImageData(0, 0, 256, 256), d = img.data;
  for (let i = 0; i < d.length; i += 4) { const n = (Math.random() - 0.5) * 26; d[i] += n; d[i + 1] += n; d[i + 2] += n * 0.8; }
  x.putImageData(img, 0, 0);
  x.strokeStyle = 'rgba(120,95,60,.18)'; x.lineWidth = 2;
  for (let y = 0; y < 256; y += 11) { x.beginPath(); for (let k = 0; k <= 256; k += 8) x.lineTo(k, y + Math.sin(k * 0.05 + y) * 4); x.stroke(); }
  const t = new THREE.CanvasTexture(c); t.wrapS = t.wrapT = THREE.RepeatWrapping; t.repeat.set(90, 90); t.anisotropy = 4; return t;
})();
for (let i = 0; i < gc.length; i++) gc[i] = Math.min(1, gc[i] * 0.45 + 0.6);
gGeo.setAttribute('color', new THREE.Float32BufferAttribute(gc, 3));
const ground = new THREE.Mesh(gGeo, new THREE.MeshLambertMaterial({ vertexColors: true, map: sandTex }));
/* رمل حقيقي من مجسم Desert Landscape: كل بلاطة 100 وحدة، تتكرر بدون فواصل */
loadTex(['sand.jpg'], t => { t.wrapS = t.wrapT = THREE.RepeatWrapping; t.repeat.set(80, 80); t.anisotropy = 4; ground.material.map = t; ground.material.needsUpdate = true; });
scene.add(ground);

/* القاعدة الجوية: مدرج مرتفع وحظيرة */
const RY = 26, RW = 80, RZ0 = -520, RZ1 = 1400, HZ = -470;
const base = new THREE.Group();
const concrete = new THREE.MeshLambertMaterial({ color: 0x6d7178 });
const slab = new THREE.Mesh(new THREE.BoxGeometry(RW, RY, RZ1 - RZ0), concrete);
slab.position.set(0, RY / 2, (RZ0 + RZ1) / 2); base.add(slab);
const lineMat = new THREE.MeshBasicMaterial({ color: 0xf2f2f2 });
/* سطح المدرج: خامة مرسومة من مجسم RUNWAY (أرقام 09 و27، أسهم، خطوط) */
const rwLen = RZ1 - (HZ + 30), rwGeo = new THREE.PlaneGeometry(RW, rwLen);
rwGeo.rotateX(-Math.PI / 2); rwGeo.rotateY(Math.PI);
const rwMat = new THREE.MeshLambertMaterial({ color: 0x2a2a2c });
const rwTop = new THREE.Mesh(rwGeo, rwMat); rwTop.position.set(0, RY + 0.08, HZ + 30 + rwLen / 2); base.add(rwTop);
loadTex(['runway_tex.jpg'], t => { rwMat.map = t; rwMat.color.setHex(0xffffff); rwMat.needsUpdate = true; });
/* الحظيرة: مغلقة ومرتبة مثل حظيرة حقيقية */
const addBox = (w, h, d, mat, x, y, z) => { const m = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat); m.position.set(x, y, z); base.add(m); return m; };
const wallMat = new THREE.MeshLambertMaterial({ color: 0x9ea3a8, side: THREE.DoubleSide });
const doorMat = new THREE.MeshLambertMaterial({ color: 0x6f7780 });
const metal = new THREE.MeshLambertMaterial({ color: 0x4a5058 });
const yellow = new THREE.MeshLambertMaterial({ color: 0xe0b030 });
const lamp = new THREE.MeshBasicMaterial({ color: 0xfff4d6 });
const win = new THREE.MeshBasicMaterial({ color: 0xbcd6e8 });
const hW = 70, hH = 30, hD = 60, hz0 = HZ - hD / 2, hz1 = HZ + hD / 2;
addBox(hW, hH, 1.5, wallMat, 0, RY + hH / 2, hz0);                                   // الجدار الخلفي
[-1, 1].forEach(s => addBox(1.5, hH, hD, wallMat, s * hW / 2, RY + hH / 2, HZ));     // الجدران الجانبية
[-1, 1].forEach(s => addBox(hW / 2 - 0.4, hH - 3, 1.2, doorMat, s * hW / 4, RY + (hH - 3) / 2, hz1)); // الأبواب المغلقة
addBox(hW, 3, 1.5, wallMat, 0, RY + hH - 1.5, hz1);
for (let i = 0; i < 12; i++) addBox(0.5, hH - 4, 0.4, metal, -hW / 2 + 3 + i * 5.8, RY + (hH - 3) / 2, hz1 - 0.9);
addBox(hW + 4, 1.5, hD + 4, new THREE.MeshLambertMaterial({ color: 0x5d636b }), 0, RY + hH, HZ); // السقف
for (let i = -2; i <= 2; i++) addBox(hW, 1, 1, metal, 0, RY + hH - 1.4, HZ + i * 12);
addBox(hW - 2, 0.3, hD - 2, new THREE.MeshLambertMaterial({ color: 0xb4b6b3 }), 0, RY + 0.15, HZ); // الأرضية
[-15, 15].forEach(x => addBox(0.6, 0.05, hD - 8, yellow, x, RY + 0.33, HZ));
addBox(0.5, 0.05, hD - 14, new THREE.MeshBasicMaterial({ color: 0xf2f2f2 }), 0, RY + 0.33, HZ + 4);
[-20, -5, 10, 25].forEach(z => [-15, 15].forEach(x => addBox(6, 0.4, 1.6, lamp, x, RY + hH - 2.2, HZ + z))); // أضواء السقف
[-1, 1].forEach(s => addBox(0.3, 4, hD - 10, win, s * (hW / 2 - 0.9), RY + hH - 7, HZ));                  // الشبابيك
addBox(hW - 4, 0.6, 6, metal, 0, RY + 12, hz0 + 3.5);                                // الممر العلوي
addBox(hW - 4, 1.1, 0.25, yellow, 0, RY + 13.2, hz0 + 6.4);
for (let x = -30; x <= 30; x += 12) addBox(0.6, 12, 0.6, metal, x, RY + 6, hz0 + 6.2);
for (let i = 0; i < 12; i++) addBox(4, i + 1, 1.3, metal, -30, RY + (i + 1) / 2, hz0 + 6.5 + (12 - i) * 1.3); // السلالم
[-20, 20].forEach(x => addBox(1, 1.2, hD - 4, metal, x, RY + hH - 4, HZ));          // الرافعة العلوية
addBox(42, 1.6, 1.6, yellow, 0, RY + hH - 4.6, HZ - 4);
addBox(2.4, 2.2, 2.4, metal, 0, RY + hH - 6.4, HZ - 4);
addBox(0.15, 9, 0.15, metal, 0, RY + hH - 12, HZ - 4);
const shelfMat = new THREE.MeshLambertMaterial({ color: 0x35507a });
const boxMats = [0x9a7b4f, 0x6b7a4a, 0x8a5a3a].map(c => new THREE.MeshLambertMaterial({ color: c }));
[-14, 0, 14].forEach((z, j) => {                                                      // الرفوف
  addBox(3, 10, 0.4, shelfMat, hW / 2 - 3, RY + 5, HZ + z - 5);
  addBox(3, 10, 0.4, shelfMat, hW / 2 - 3, RY + 5, HZ + z + 5);
  [2.5, 6, 9.5].forEach((y, k) => { addBox(3, 0.3, 10, shelfMat, hW / 2 - 3, RY + y, HZ + z); addBox(2.4, 2.4, 3, boxMats[(j + k) % 3], hW / 2 - 3, RY + y + 1.35, HZ + z + (k - 1) * 2.5); });
});
const cart = new THREE.MeshLambertMaterial({ color: 0xc0392b });
[[-22, 18], [-26, 18], [22, -20]].forEach(([x, z]) => addBox(3, 3.2, 2, cart, x, RY + 1.9, HZ + z)); // عربات العدة
[[24, -24], [28, -24], [26, -24]].forEach(([x, z], i) => addBox(4, 4, 4, boxMats[i], x, RY + 2 + (i === 2 ? 4 : 0), HZ + z));
const truck = addBox(5, 4, 9, new THREE.MeshLambertMaterial({ color: 0x5b6b3a }), 26, RY + 2.2, HZ + 12); // شاحنة الوقود
addBox(4.6, 2.6, 4, new THREE.MeshLambertMaterial({ color: 0x3e4a28 }), 26, RY + 5.4, HZ + 14);
const hose = new THREE.Mesh(new THREE.CylinderGeometry(0.25, 0.25, 11, 6), new THREE.MeshLambertMaterial({ color: 0x222222 })); hose.rotation.z = Math.PI / 2; hose.position.set(18, RY + 2.2, HZ + 8); base.add(hose);
const tower = new THREE.Mesh(new THREE.BoxGeometry(8, 46, 8), new THREE.MeshLambertMaterial({ color: 0xd9d4c7 })); tower.position.set(-56, RY + 23, -300); base.add(tower);
const towerPad = new THREE.Mesh(new THREE.BoxGeometry(30, RY, 30), concrete); towerPad.position.set(-56, RY / 2, -300); base.add(towerPad);
const cab = new THREE.Mesh(new THREE.CylinderGeometry(9, 7, 7, 8), new THREE.MeshLambertMaterial({ color: 0x2d4a63 })); cab.position.set(-56, RY + 49, -300); base.add(cab);
const beacon = new THREE.Mesh(new THREE.SphereGeometry(3.5, 8, 6), new THREE.MeshBasicMaterial({ color: 0xff3a2a, fog: false })); beacon.position.set(-56, RY + 56, -300); base.add(beacon);
const padLight = new THREE.MeshBasicMaterial({ color: 0x9fd0ff, fog: false });
for (let z = -400; z < RZ1; z += 50) [-1, 1].forEach(s => { const l = new THREE.Mesh(new THREE.BoxGeometry(2, 1.2, 2), padLight); l.position.set(s * (RW / 2 - 1), RY + 0.6, z); base.add(l); });
const hLight = new THREE.PointLight(0xffe8c8, 1.3, 160); hLight.position.set(0, RY + 24, HZ); base.add(hLight);
scene.add(base);
const inBase = (x, z) => Math.abs(x) < RW / 2 && z > RZ0 && z < RZ1;
const surfaceH = (x, z) => inBase(x, z) ? RY : groundH(x, z);

/* الغيوم */
const cloudMats = [0xffffff, 0xf2f4f6, 0xe4e8ec].map(c => new THREE.SpriteMaterial({ map: softTex, color: c, transparent: true, opacity: 0.8, depthWrite: false }));
const clouds = [];
for (let i = 0; i < 26; i++) {
  const g = new THREE.Group();
  for (let j = 0; j < 9; j++) { const m = new THREE.Sprite(cloudMats[j % 3]); const s = rand(110, 230); m.scale.set(s * 1.5, s * 0.8, 1); m.position.set(rand(-150, 150), rand(-20, 25), rand(-90, 90)); g.add(m); }
  g.position.set(rand(-2500, 2500), rand(350, 900), rand(-2500, 2500));
  scene.add(g); clouds.push(g);
}

/* ---------- مجسم الطائرة الحقيقي (GLB) ---------- */
let F16 = null, SAM = null, enemyMats = new Map();
const MODEL_S = 0.14, MODEL_CX = 25.7;
function makePlane(body, stripes, type) {
  if (!F16) return makePlaneBasic(body, stripes, type);
  const g = new THREE.Group();
  const m = F16.clone(true);
  m.scale.setScalar(MODEL_S); m.rotation.y = -Math.PI / 2; m.position.z = -MODEL_CX * MODEL_S;
  const isEnemy = body === 0x3d444d;
  const gearOn = [], gearOff = [];
  m.traverse(o => {
    if (/landingOn/.test(o.name)) gearOn.push(o);
    if (/landingOff/.test(o.name)) gearOff.push(o);
    if (o.isMesh && isEnemy) {
      if (!enemyMats.has(o.material)) { const c = o.material.clone(); c.color.setHex(0x6b7078); enemyMats.set(o.material, c); }
      o.material = enemyMats.get(o.material);
    }
  });
  g.add(m);
  // علم الدولة على الذيل
  stripes.forEach((c, k) => {
    const st = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.45, 1.3), new THREE.MeshLambertMaterial({ color: c }));
    st.position.set(0, 3.25 - k * 0.45, -8.3); g.add(st);
  });
  const glow = new THREE.Mesh(new THREE.CylinderGeometry(0.55, 0.4, 0.4, 10), new THREE.MeshBasicMaterial({ color: 0xffc463 }));
  glow.rotation.x = Math.PI / 2; glow.position.set(0, 0.2, -10.6); g.add(glow);
  g.userData.glow = glow;
  g.userData.setGear = down => { gearOn.forEach(o => o.visible = down); gearOff.forEach(o => o.visible = !down); };
  g.userData.setGear(false);
  g.scale.setScalar(type.scale);
  g.rotation.order = 'YXZ';
  scene.add(g);
  return g;
}
function loadSam() {
  if (!THREE.GLTFLoader) return;
  const urls = ['/_blob/6c9bc4e91f61ea90f9d3c839d574ee43', 'sam_small.glb'];
  urls.reverse(); // الملف المحلي أولاً (Acode وGitHub Pages)، وبعده النسخة المرفوعة على Claude
  const go = i => { if (i >= urls.length) return; new THREE.GLTFLoader().load(urls[i], g => {
    g.scene.traverse(o => { if (o.isMesh) { const ms = Array.isArray(o.material) ? o.material : [o.material]; ms.forEach(mt => { if (mt.map) mt.map.encoding = THREE.LinearEncoding; mt.needsUpdate = true; }); } });
    SAM = g.scene;
  }, undefined, () => go(i + 1)); };
  go(0);
}
function loadModel() {
  loadSam();
  if (!THREE.GLTFLoader) { $('loadTxt').textContent = 'ما قدرت أحمّل أداة المجسمات، اللعبة تشتغل بالطائرة البسيطة'; show('spin', false); show('bGo', true); return; }
  // على رابط Claude: الملف مرفوع مع الصفحة. على Acode: ملف f16.glb جنب index.html
  const urls = ['/_blob/5dc83b467accc4eda8a71e1369afd668', 'f16.glb'];
  urls.reverse(); // الملف المحلي أولاً (Acode وGitHub Pages)، وبعده النسخة المرفوعة على Claude
  tryLoad(urls, 0);
}
function tryLoad(urls, i) {
  if (i >= urls.length) { $('loadTxt').textContent = 'ما لقيت ملف f16.glb، اللعبة تشتغل بالطائرة البسيطة'; show('spin', false); show('bGo', true); return; }
  new THREE.GLTFLoader().load(urls[i], gltf => {
    const sc = gltf.scene;
    sc.traverse(o => {
      if (!o.isMesh) return;
      const mats = Array.isArray(o.material) ? o.material : [o.material];
      mats.forEach(mt => {
        ['map', 'emissiveMap'].forEach(k => { if (mt[k]) mt[k].encoding = THREE.LinearEncoding; });
        if (envMap) { mt.envMap = envMap; mt.envMapIntensity = 0.9; }
        mt.needsUpdate = true;
      });
    });
    F16 = sc;
    $('loadTxt').textContent = 'تم تحميل الطائرة ✓'; show('spin', false); show('bGo', true);
    if (player.obj) { const p = player.obj.position.clone(), r = player.obj.rotation.clone(), v = player.obj.visible; buildPlayerPlane(); player.obj.position.copy(p); player.obj.rotation.copy(r); player.obj.visible = v; }
  }, xhr => { if (xhr.total) $('loadTxt').textContent = 'جاري تحميل مجسم الطائرة... ' + Math.round(xhr.loaded / xhr.total * 100) + '%'; },
  () => tryLoad(urls, i + 1));
}
/* الطائرة البسيطة: احتياط إذا ما انحمل المجسم */
function makePlaneBasic(body, stripes, type) {
  const g = new THREE.Group();
  const mat = new THREE.MeshLambertMaterial({ color: body, flatShading: true });
  const dark = new THREE.MeshLambertMaterial({ color: 0x1d2a3a, flatShading: true });
  const fus = new THREE.Mesh(new THREE.CylinderGeometry(1.2, 1.6, 14, 8), mat); fus.rotation.x = Math.PI / 2; g.add(fus);
  const nose = new THREE.Mesh(new THREE.ConeGeometry(1.2, 5, 8), mat); nose.rotation.x = Math.PI / 2; nose.position.z = 9.5; g.add(nose);
  const cock = new THREE.Mesh(new THREE.SphereGeometry(1, 8, 6), dark); cock.scale.set(0.9, 0.8, 2.2); cock.position.set(0, 1.1, 3.5); g.add(cock);
  const span = type.fins === 1 ? 11 : 10;
  const ws = new THREE.Shape();
  [[0, 5], [span, -2], [span, -4], [-span, -4], [-span, -2]].forEach((p, i) => i ? ws.lineTo(p[0], -p[1]) : ws.moveTo(p[0], -p[1]));
  ws.lineTo(0, -5);
  const wGeo = new THREE.ExtrudeGeometry(ws, { depth: 0.35, bevelEnabled: false }); wGeo.rotateX(-Math.PI / 2);
  const wing = new THREE.Mesh(wGeo, mat); wing.position.set(0, -0.3, -1); g.add(wing);
  const stab = new THREE.Mesh(new THREE.BoxGeometry(8, 0.25, 2.4), mat); stab.position.set(0, 0, -6.4); g.add(stab);
  const finXs = type.fins === 1 ? [0] : [-1.3, 1.3];
  finXs.forEach(x => {
    const tilt = x === 0 ? 0 : -Math.sign(x) * 0.25;
    const fin = new THREE.Mesh(new THREE.BoxGeometry(0.3, 4.4, 3), mat); fin.position.set(x, 2.4, -6); fin.rotation.z = tilt; g.add(fin);
    stripes.forEach((c, k) => {
      const st = new THREE.Mesh(new THREE.BoxGeometry(0.36, 0.55, 2.2), new THREE.MeshLambertMaterial({ color: c }));
      st.position.set(x + (x === 0 ? 0 : Math.sign(x) * 0.25), 3.6 - k * 0.55, -6.1); st.rotation.z = tilt; g.add(st);
    });
  });
  [-1, 1].forEach(s => { const tip = new THREE.Mesh(new THREE.BoxGeometry(1.6, 0.4, 1.4), new THREE.MeshLambertMaterial({ color: stripes[0] })); tip.position.set(s * (span - 1.2), -0.1, -4.2); g.add(tip); });
  const glow = new THREE.Mesh(new THREE.CylinderGeometry(1.05, 0.8, 0.6, 8), new THREE.MeshBasicMaterial({ color: 0xffc463 })); glow.rotation.x = Math.PI / 2; glow.position.z = -7.2; g.add(glow);
  g.userData.glow = glow;
  g.scale.setScalar(type.scale);
  g.rotation.order = 'YXZ';
  scene.add(g);
  return g;
}
const fwdOf = (yaw, pitch, out) => out.set(Math.sin(yaw) * Math.cos(pitch), Math.sin(pitch), Math.cos(yaw) * Math.cos(pitch));

/* ---------- المؤثرات ---------- */
const tmpV = V(), tmpV2 = V(), tmpV3 = V();
const tq = new THREE.Quaternion(), AX = V(), UPW = new THREE.Vector3(0, 1, 0);
const bulletGeo = new THREE.BoxGeometry(0.35, 0.35, 7);
const bulletMatP = new THREE.MeshBasicMaterial({ color: 0xffe27a });
const bulletMatE = new THREE.MeshBasicMaterial({ color: 0xff7a5a });
const bullets = [];
function spawnBullet(owner, pos, dir, speed) {
  let b = bullets.find(x => !x.alive);
  if (!b) { b = { mesh: new THREE.Mesh(bulletGeo, bulletMatP), pos: V(), dir: V() }; scene.add(b.mesh); bullets.push(b); }
  b.alive = true; b.owner = owner; b.life = 1.4; b.speed = speed;
  b.pos.copy(pos); b.dir.copy(dir);
  b.mesh.material = owner === 'p' ? bulletMatP : bulletMatE;
  b.mesh.visible = true; b.mesh.position.copy(pos);
  b.mesh.lookAt(tmpV.copy(pos).add(dir));
}
const missileGeo = new THREE.CylinderGeometry(0.4, 0.4, 5, 6); missileGeo.rotateX(Math.PI / 2);
const missileMat = new THREE.MeshLambertMaterial({ color: 0xeeeeee });
const missiles = [];
function spawnMissile(owner, pos, dir, target) {
  // يبدأ بسرعة الطائرة اللي أطلقته ويتسارع، ويلف بحد أقصى (ما يلف فجأة)
  const isP = owner === player;
  const m = { mesh: new THREE.Mesh(missileGeo, missileMat), pos: pos.clone(), prev: pos.clone(), dir: dir.clone().normalize(), owner, target, life: 7,
    speed: (owner.speed || 0) + 40, maxSpeed: isP ? 340 : 300, turn: isP ? 2.4 : owner.isGround ? 1.9 : 1.7, smokeT: 0, alive: true, lost: false };
  scene.add(m.mesh); missiles.push(m);
  return m;
}
const flareGeo = new THREE.SphereGeometry(1.4, 6, 4);
const flareMat = new THREE.MeshBasicMaterial({ color: 0xffd060 });
const flares = [];
function dropFlares(owner) {
  for (let i = 0; i < 4; i++) {
    const f = { mesh: new THREE.Mesh(flareGeo, flareMat), pos: owner.pos.clone(), vel: V(), life: 3, alive: true, isFlare: true };
    fwdOf(owner.yaw, owner.pitch, f.vel).multiplyScalar(owner.speed * 0.4);
    f.vel.x += rand(-30, 30); f.vel.y += rand(-20, 5); f.vel.z += rand(-30, 30);
    scene.add(f.mesh); flares.push(f);
    if (i === 0) missiles.forEach(m => { if (m.alive && m.target === owner && Math.random() < 0.78) m.target = f; });
  }
}
const fx = [];
const FIRE = new Set([0xff8a3d, 0xffe08a, 0xffd080, 0xffb050]);
function spawnFx(pos, size, color, life, grow) {
  let e = fx.find(x => !x.alive);
  if (!e) { e = { mesh: new THREE.Sprite(new THREE.SpriteMaterial({ map: softTex, transparent: true, depthWrite: false })) }; scene.add(e.mesh); fx.push(e); }
  e.alive = true; e.t = 0; e.life = life; e.size = size; e.grow = grow;
  e.mesh.material.color.setHex(color);
  e.mesh.material.blending = FIRE.has(color) ? THREE.AdditiveBlending : THREE.NormalBlending;
  e.mesh.material.needsUpdate = true;
  e.mesh.position.copy(pos); e.mesh.visible = true;
}
function explode(pos, big) {
  spawnFx(pos, big ? 14 : 6, 0xff8a3d, 0.9, 2.2);
  spawnFx(pos, big ? 9 : 4, 0xffe08a, 0.5, 2.5);
  for (let i = 0; i < (big ? 6 : 2); i++) spawnFx(tmpV.copy(pos).add(V().set(rand(-8, 8), rand(-8, 8), rand(-8, 8))), rand(5, 9), 0x3a3a3a, 1.8, 1.8);
  sfx.boom(big);
}

/* ---------- الصوت ---------- */
let AC = null;
const sfx = {
  init() { if (AC) { if (AC.state === 'suspended') AC.resume(); return; } try { AC = new (window.AudioContext || window.webkitAudioContext)(); this.startEngine(); } catch (e) { AC = null; } },
  tone(f, d, type, vol) {
    if (!AC) return;
    const o = AC.createOscillator(), g = AC.createGain();
    o.type = type; o.frequency.value = f; g.gain.value = vol;
    g.gain.exponentialRampToValueAtTime(0.0001, AC.currentTime + d);
    o.connect(g).connect(AC.destination); o.start(); o.stop(AC.currentTime + d);
  },
  boom(big) {
    if (!AC) return;
    const len = AC.sampleRate * (big ? 1.4 : 0.6), buf = AC.createBuffer(1, len, AC.sampleRate), d = buf.getChannelData(0);
    let br = 0;
    for (let i = 0; i < len; i++) { br = (br + 0.04 * (Math.random() * 2 - 1)) / 1.04; d[i] = (br * 6 + (Math.random() * 2 - 1) * 0.25) * Math.pow(1 - i / len, 2.2); }
    const s = AC.createBufferSource(), g = AC.createGain(), f = AC.createBiquadFilter();
    f.type = 'lowpass'; f.frequency.value = big ? 500 : 800; g.gain.value = big ? 0.9 : 0.45;
    s.buffer = buf; s.connect(f).connect(g).connect(AC.destination); s.start();
  },
  noise(dur, type, freq, vol, sweepTo) {
    if (!AC) return;
    const len = Math.floor(AC.sampleRate * dur), buf = AC.createBuffer(1, len, AC.sampleRate), d = buf.getChannelData(0);
    for (let i = 0; i < len; i++) d[i] = (Math.random() * 2 - 1) * (1 - i / len);
    const s = AC.createBufferSource(), g = AC.createGain(), f = AC.createBiquadFilter();
    f.type = type; f.frequency.value = freq; if (sweepTo) f.frequency.exponentialRampToValueAtTime(sweepTo, AC.currentTime + dur);
    g.gain.value = vol; s.buffer = buf; s.connect(f).connect(g).connect(AC.destination); s.start();
  },
  gun() { this.noise(0.06, 'bandpass', 1400, 0.22); },
  whoosh(v) { this.noise(1.1, 'bandpass', 2400, 0.35 * v, 300); this.noise(0.25, 'lowpass', 300, 0.4 * v); },
  flare() { this.noise(0.35, 'highpass', 2500, 0.18); this.tone(1800, 0.12, 'triangle', 0.03); },
  /* صوت المحرك النفاث: ضجيج منخفض + صفير توربين، يتغير مع عتلة السرعة */
  engine: null,
  startEngine() {
    if (!AC || this.engine) return;
    const len = AC.sampleRate * 2, buf = AC.createBuffer(1, len, AC.sampleRate), d = buf.getChannelData(0);
    let br = 0;
    for (let i = 0; i < len; i++) { br = (br + 0.02 * (Math.random() * 2 - 1)) / 1.02; d[i] = br * 3.5 + (Math.random() * 2 - 1) * 0.08; }
    const src = AC.createBufferSource(); src.buffer = buf; src.loop = true;
    const lp = AC.createBiquadFilter(); lp.type = 'lowpass'; lp.frequency.value = 300;
    const g = AC.createGain(); g.gain.value = 0;
    src.connect(lp).connect(g).connect(AC.destination); src.start();
    const osc = AC.createOscillator(); osc.type = 'sawtooth'; osc.frequency.value = 150;
    const bp = AC.createBiquadFilter(); bp.type = 'bandpass'; bp.frequency.value = 2200; bp.Q.value = 6;
    const g2 = AC.createGain(); g2.gain.value = 0;
    osc.connect(bp).connect(g2).connect(AC.destination); osc.start();
    this.engine = { lp, g, osc, bp, g2 };
  },
  setEngine(thr, level) {
    const e = this.engine; if (!e) return;
    const t = AC.currentTime;
    e.g.gain.setTargetAtTime(level * (0.06 + thr * 0.12), t, 0.25);
    e.lp.frequency.setTargetAtTime(220 + thr * 1300, t, 0.25);
    e.osc.frequency.setTargetAtTime(120 + thr * 300, t, 0.3);
    e.bp.frequency.setTargetAtTime(1600 + thr * 2400, t, 0.3);
    e.g2.gain.setTargetAtTime(level * (0.004 + thr * 0.012), t, 0.25);
  },
  /* تنبيهات القمرة + نداء صوتي مثل الطائرات الحقيقية */
  say(text) {
    try {
      if (!window.speechSynthesis || speechSynthesis.speaking) return;
      const u = new SpeechSynthesisUtterance(text); u.lang = 'en-US'; u.rate = 1.05; u.pitch = 1.2; u.volume = 0.9;
      speechSynthesis.speak(u);
    } catch (e) {}
  },
  mt: 0, lt: 0, ht: 0, sayT: 0, alt2: false,
  alarms(dt, threat, low, high) {
    if (!AC) return;
    this.mt -= dt; this.lt -= dt; this.ht -= dt; this.sayT -= dt;
    if (threat) {
      if (this.mt <= 0) { this.alt2 = !this.alt2; this.tone(this.alt2 ? 1500 : 1100, 0.09, 'square', 0.05); this.mt = 0.12; }
      if (this.sayT <= 0) { this.say('Missile, missile'); this.sayT = 3; }
    } else if (low) {
      if (this.lt <= 0) { this.tone(520, 0.2, 'sawtooth', 0.05); this.lt = 0.4; }
      if (this.sayT <= 0) { this.say('Pull up, pull up'); this.sayT = 2.6; }
    } else if (high) {
      if (this.ht <= 0) { this.tone(880, 0.3, 'sine', 0.04); this.ht = 1; }
      if (this.sayT <= 0) { this.say('Altitude, altitude'); this.sayT = 4; }
    }
  }
};

/* ---------- اللاعب والأعداء ---------- */
const player = { obj: null, pos: V(), vel: V(), yaw: 0, pitch: 0, roll: 0, yawRate: 0, pitchRate: 0, speed: 0, throttle: 0.6, gunCd: 0, alive: true };
const NAMES = ['Pilot22', 'صقر الشمال', 'Viper', 'الشبح', 'Raptor', 'النسر الأسود', 'Shadow', 'Falcon9'];
const enemies = [];
/* ---------- أهداف أرضية: دبابات، مدافع مضادة للطائرات، منصات صواريخ أرض جو ---------- */
const grounds = [], wrecks = [];
const gSand = new THREE.MeshLambertMaterial({ color: 0x8a7a52, flatShading: true });
const gDark = new THREE.MeshLambertMaterial({ color: 0x3d3a2e, flatShading: true });
const gWhite = new THREE.MeshLambertMaterial({ color: 0xe8e8e8, flatShading: true });
const wreckMat = new THREE.MeshLambertMaterial({ color: 0x1f1d1a, flatShading: true });
const GKIND = { tank: { name: 'دبابة', hp: 60 }, aa: { name: 'مدفع مضاد للطائرات', hp: 80 }, sam: { name: 'منصة صواريخ أرض جو', hp: 100 } };
function gPart(parent, geo, mat, x, y, z, rx) { const m = new THREE.Mesh(geo, mat); m.position.set(x, y, z); if (rx) m.rotation.x = rx; parent.add(m); return m; }
function makeGround(kind) {
  const g = new THREE.Group(), tur = new THREE.Group();
  if (kind === 'tank') {
    gPart(g, new THREE.BoxGeometry(7, 2.4, 10), gSand, 0, 1.8, 0);
    [-1, 1].forEach(sd => gPart(g, new THREE.BoxGeometry(1.8, 2, 10.6), gDark, sd * 3.8, 1, 0));
    gPart(tur, new THREE.BoxGeometry(4.4, 1.8, 5), gSand, 0, 3.9, -0.5);
    const bar = new THREE.CylinderGeometry(0.35, 0.35, 7, 6); bar.rotateX(Math.PI / 2);
    gPart(tur, bar, gDark, 0, 3.9, 4.5);
  } else if (kind === 'aa') {
    gPart(g, new THREE.CylinderGeometry(4, 5, 2, 8), gDark, 0, 1, 0);
    gPart(tur, new THREE.BoxGeometry(3.4, 2.6, 3.4), gSand, 0, 3.3, 0);
    const bar = new THREE.CylinderGeometry(0.25, 0.25, 8, 6); bar.rotateX(Math.PI / 2);
    [-0.8, 0.8].forEach(x => gPart(tur, bar, gDark, x, 5.6, 3, -0.8));
  } else if (SAM) {
    // منصة صواريخ حقيقية من مجسم Sevom Khordad SAM
    const m = SAM.clone(true); m.scale.setScalar(145); g.add(m);
  } else {
    gPart(g, new THREE.BoxGeometry(5, 3, 11), gSand, 0, 2.2, 0);
    gPart(g, new THREE.BoxGeometry(5, 3, 3), gDark, 0, 2.7, 6.8);
    const rack = new THREE.Group(); rack.position.set(0, 5, -1); rack.rotation.x = -0.55; tur.add(rack);
    gPart(rack, new THREE.BoxGeometry(4.4, 1.6, 7.5), gDark, 0, 0, 0);
    const tube = new THREE.CylinderGeometry(0.45, 0.45, 7, 6); tube.rotateX(Math.PI / 2);
    [-1.2, 1.2].forEach(x => gPart(rack, tube, gWhite, x, 1.2, 0));
  }
  g.add(tur); g.userData.tur = tur;
  scene.add(g);
  return g;
}
function spawnGroundMission(count, withSam) {
  let cx, cz;
  for (let k = 0; k < 20; k++) {
    const a = rand(0, Math.PI * 2), d = rand(1600, 2300);
    cx = player.pos.x + Math.sin(a) * d; cz = player.pos.z + Math.cos(a) * d;
    if (Math.hypot(cx, cz - 150) > 1200) break;
  }
  G.missionC = V().set(cx, 0, cz);
  for (let i = 0; i < count; i++) {
    const kind = i === 0 && (withSam === undefined ? G.wave >= 4 : withSam) ? 'sam' : i % 3 === 1 ? 'aa' : 'tank';
    const x = cx + rand(-160, 160), z = cz + rand(-160, 160), y = groundH(x, z);
    const t = { obj: makeGround(kind), pos: V().set(x, y + 2.5, z), kind, name: GKIND[kind].name, hp: GKIND[kind].hp, maxHp: GKIND[kind].hp, alive: true, isGround: true, fireCd: rand(1, 3), burst: 0, mslCd: rand(5, 10) };
    t.obj.position.set(x, y, z); t.obj.rotation.y = rand(0, Math.PI * 2);
    grounds.push(t);
  }
}
function updateGround(t, dt) {
  const d = tmpV2.copy(player.pos).sub(t.pos), dist = d.length();
  const tur = t.obj.userData.tur;
  const want = Math.atan2(d.x, d.z) - t.obj.rotation.y;
  tur.rotation.y += wrapA(want - tur.rotation.y) * Math.min(1, dt * 2);
  if (!(player.alive && G.mode === 'fly')) return;
  t.fireCd -= dt; t.mslCd -= dt;
  const muzzle = t.pos.clone().add(V().set(0, 4, 0));
  const aimAt = (spd, spread) => {
    const lead = player.pos.clone().addScaledVector(player.vel, dist / spd).sub(muzzle).normalize();
    return lead.add(V().set(rand(-spread, spread), rand(-spread, spread), rand(-spread, spread))).normalize();
  };
  if (t.kind === 'aa' && dist < 950 && player.pos.y > t.pos.y + 25 && t.fireCd <= 0) {
    spawnBullet('e', muzzle, aimAt(650, 0.035), 650);
    t.burst++; t.fireCd = t.burst >= 6 ? rand(1.4, 2.4) : 0.12; if (t.burst >= 6) t.burst = 0;
  } else if (t.kind === 'tank' && dist < 650 && t.fireCd <= 0) {
    spawnBullet('e', muzzle, aimAt(450, 0.03), 450);
    t.fireCd = rand(2.5, 4);
  } else if (t.kind === 'sam' && dist < 1600 && t.mslCd <= 0 && missiles.filter(m => m.alive && m.owner !== player).length < 2) {
    spawnMissile(t, t.pos.clone().add(V().set(0, 8, 0)), V().set(0, 1, 0).add(d.clone().normalize()).normalize(), player);
    t.mslCd = rand(12, 18);
    addFeed('صاروخ أرض جو انطلق عليك!');
  }
}
function damageGround(t, dmg, how) {
  if (!t.alive) return;
  t.hp -= dmg;
  spawnFx(t.pos, 2.5, 0xffd080, 0.3, 2);
  if (t.hp > 0) return;
  t.alive = false;
  explode(t.pos, true);
  scene.remove(t.obj);
  const w = new THREE.Mesh(new THREE.BoxGeometry(6, 1.5, 8), wreckMat);
  w.position.set(t.pos.x, t.pos.y - 1.8, t.pos.z); w.rotation.y = rand(0, 3); scene.add(w); wrecks.push(w);
  G.kills++; if (!(G.mission && G.mission.free)) G.coins += 40;
  addFeed(`<b>${me()}</b> دمّر ${t.name}${how}`);
}
function spawnEnemy(skill) {
  const a = rand(0, Math.PI * 2), d = rand(900, 1300);
  const e = {
    obj: makePlane(0x3d444d, [0xd6332a, 0x222222, 0xd6332a], TYPES[0]),
    pos: V().set(player.pos.x + Math.sin(a) * d, rand(220, 520), player.pos.z + Math.cos(a) * d),
    vel: V(), yaw: 0, pitch: 0, roll: 0, speed: 150, hp: 100, alive: true, isEnemy: true,
    name: NAMES[Math.floor(Math.random() * NAMES.length)], skill,
    state: 'chase', t: 0, jy: 0, jp: 0, fireCd: rand(1, 3), burst: 0, lockT: 0, mslCd: rand(6, 12), flrCd: 0
  };
  e.yaw = Math.atan2(player.pos.x - e.pos.x, player.pos.z - e.pos.z);
  enemies.push(e);
}
function applyPlane(p, dt) {
  fwdOf(p.yaw, p.pitch, tmpV);
  p.vel.copy(tmpV).multiplyScalar(p.speed);
  p.pos.addScaledVector(p.vel, dt);
  p.obj.position.copy(p.pos);
  p.obj.rotation.set(-p.pitch, p.yaw, p.roll);
}

function updateEnemy(e, dt) {
  const sk = e.skill, maxTurn = 0.75 + sk * 0.12;
  const toP = tmpV2.copy(player.pos).sub(e.pos);
  const dist = toP.length();
  const dirP = toP.clone().divideScalar(dist || 1);
  const f = fwdOf(e.yaw, e.pitch, tmpV3);
  const aim = f.dot(dirP);
  e.t -= dt; e.fireCd -= dt; e.mslCd -= dt; e.flrCd -= dt;
  const pAlive = player.alive && G.mode === 'fly';

  const incoming = missiles.some(m => m.alive && m.target === e && m.pos.distanceTo(e.pos) < 500);
  if (incoming && e.flrCd <= 0 && Math.random() < 0.5 + sk * 0.08) { dropFlares(e); e.flrCd = 6; }
  if ((incoming || (aim < -0.55 && dist < 380)) && e.state !== 'evade') { e.state = 'evade'; e.t = rand(2.5, 4); e.jy = 0; }
  if (e.state === 'chase' && dist < 90) { e.state = 'extend'; e.t = 2; }

  let dYaw, dPitch, tSpeed;
  if (e.state === 'evade') {
    if (e.jy === 0 || Math.random() < dt * 0.9) { e.jy = e.yaw + rand(0.9, 1.8) * (Math.random() < 0.5 ? -1 : 1); e.jp = rand(-0.45, 0.5); }
    dYaw = e.jy; dPitch = e.jp; tSpeed = 195;
    if (e.t <= 0) e.state = 'chase';
  } else if (e.state === 'extend') {
    // يبعد عنك بزاوية حتى ما يصطدم بيك
    dYaw = e.yaw + 0.6; dPitch = 0.3; tSpeed = 190;
    if (e.t <= 0) e.state = 'chase';
  } else {
    const lead = tmpV.copy(player.pos).addScaledVector(player.vel, dist / 750 * (0.6 + sk * 0.1));
    const d = lead.sub(e.pos);
    dYaw = Math.atan2(d.x, d.z);
    dPitch = Math.atan2(d.y, Math.hypot(d.x, d.z));
    tSpeed = clamp(player.speed + (dist > 400 ? 40 : 5), 90, 200);
  }
  const alt = e.pos.y - surfaceH(e.pos.x, e.pos.z);
  if (alt < 130) dPitch = Math.max(dPitch, 0.55);
  if (e.pos.y > 1300) dPitch = Math.min(dPitch, -0.2);

  const yr = clamp(wrapA(dYaw - e.yaw) * 2.2, -maxTurn, maxTurn);
  e.yaw += yr * dt;
  e.pitch += clamp((clamp(dPitch, -0.9, 0.9) - e.pitch) * 2, -0.9, 0.9) * dt;
  e.roll += (-yr / maxTurn * 1.05 - e.roll) * Math.min(1, dt * 4);
  e.speed += (tSpeed - e.speed) * Math.min(1, dt * 0.8);
  applyPlane(e, dt);

  if (pAlive && e.state === 'chase' && aim > 0.985 && dist < 480 && e.fireCd <= 0) {
    const spread = 0.05 - sk * 0.006;
    spawnBullet('e', e.pos.clone().addScaledVector(f, 10), f.clone().add(V().set(rand(-spread, spread), rand(-spread, spread), rand(-spread, spread))).normalize(), 700 + e.speed);
    e.burst++;
    e.fireCd = e.burst >= 7 ? rand(1.2, 2.2) : 0.11;
    if (e.burst >= 7) e.burst = 0;
  }
  if (aim > 0.965 && dist < 750) e.lockT += dt; else e.lockT = 0;
  const live = missiles.filter(m => m.alive && m.owner !== player).length;
  if (pAlive && e.lockT > 2.6 - sk * 0.15 && e.mslCd <= 0 && live < 2) {
    spawnMissile(e, e.pos.clone().addScaledVector(f, 6).add(V().set(0, -2, 0)), f, player);
    e.mslCd = rand(10, 16) - sk; e.lockT = 0;
    addFeed(`<b>${e.name}</b> أطلق صاروخ عليك`);
  }
}

/* ---------- حالة اللعبة ---------- */
const MAX_GUN = 300, MAX_ROCKETS = 16, REFILL = 30;
const G = {
  mode: 'login', paused: false, wave: 0, kills: 0, coins: +store.get('sf_coins', 0) || 0, hp: 100, maxHp: 100,
  gun: MAX_GUN, rockets: MAX_ROCKETS, msl: 6, maxMsl: 6, flr: 3, refillT: REFILL,
  cam: 0, wpn: 0, fireCd: 0, waveT: 0, lockTarget: null, lockT: 0, locked: false, lastLockBeep: 0, warnT: 0,
  name: store.get('sf_name', ''), country: +store.get('sf_country', 0) || 0, type: +store.get('sf_type', 0) || 0, ctrl: store.get('sf_ctrl', 'easy'),
  fuel: 0, orbit: 0, didTakeoff: false, svc: 0,
  unlocked: +store.get('sf_unl', 1) || 1, avatar: +store.get('sf_av', 0) || 0,
  skins: (() => { try { return JSON.parse(store.get('sf_skins', '[0]')); } catch (e) { return [0]; } })(), skin: +store.get('sf_skin', 0) || 0,
  up: (() => { try { return Object.assign({ armor: 0, cool: 0, power: 0, reload: 0, load: 0 }, JSON.parse(store.get('sf_up', '{}'))); } catch (e) { return { armor: 0, cool: 0, power: 0, reload: 0, load: 0 }; } })(),
  mission: null, page: 0
};
G.mode = 'splash'; G.type = 0;
const saveAll = () => { store.set('sf_coins', G.coins); store.set('sf_unl', G.unlocked); store.set('sf_av', G.avatar); store.set('sf_skins', JSON.stringify(G.skins)); store.set('sf_skin', G.skin); store.set('sf_up', JSON.stringify(G.up)); store.set('sf_name', G.name); store.set('sf_country', G.country); };
/* قيم الطائرة بعد التطوير */
const stat = {
  get maxHp() { return 100 + G.up.armor * 15; },
  get gunCd() { return 0.075 * (1 - G.up.cool * 0.06); },
  get dmg() { return 1 + G.up.power * 0.12; },
  get refill() { return Math.max(16, 30 - G.up.reload * 3); },
  get msl() { return 6 + G.up.load; },
  get rockets() { return 16 + G.up.load * 2; }
};
const feedList = [];
function addFeed(html) { feedList.unshift({ html, t: 5 }); if (feedList.length > 4) feedList.pop(); }
let bigT = 0;
function bigMsg(t) { $('big').textContent = t; $('big').style.opacity = 1; bigT = 2.2; }
const me = () => G.name || 'ضيف';
const show = (id, on) => $(id).style.display = on ? '' : 'none';

function startMissionContent(m) {
  if (m.air) for (let i = 0; i < m.air; i++) spawnEnemy(m.skill || 0);
  if (m.ground) spawnGroundMission(m.ground, !!m.sam);
  if (m.escort) for (let i = 0; i < m.escort; i++) spawnEnemy(Math.max(1, m.skill || 1));
  bigMsg(`المهمة ${m.n}`);
  if (m.ground) addFeed('اتبع السهم البرتقالي للوصول للأهداف الأرضية');
}
function missionComplete() {
  const m = G.mission, reward = 100 + m.n * 60;
  G.mode = 'donewait';
  G.coins += reward;
  const first = G.unlocked <= m.n;
  G.unlocked = Math.max(G.unlocked, m.n + 1);
  saveAll();
  setTimeout(() => {
    $('doneT').textContent = `نجحت المهمة ${m.n}!`;
    $('doneP').textContent = `ربحت ${reward} عملة، ودمّرت ${G.kills} أهداف.` + (first && m.n < 11 ? ` انفتحت المهمة ${m.n + 1}.` : '') + (m.n === 11 ? ' خلّصت كل المهمات، أنت بطل صقور السماء!' : '');
    show('done', true);
  }, 1200);
}
function nextWave() {
  if (G.mission && !G.mission.free) { if (G.wave === 0) { G.wave = 1; startMissionContent(G.mission); } return; }
  G.wave++;
  G.hp = Math.min(G.maxHp, G.hp + 30);
  if (G.wave % 2 === 0) {
    // مرحلة ضرب أرض: دبابات ومدافع ومنصات صواريخ، ومن المرحلة 4 تجي طائرات حماية
    const count = Math.min(4 + G.wave / 2, 10);
    spawnGroundMission(count);
    const escort = G.wave >= 4 ? Math.min(Math.floor((G.wave - 2) / 2), 3) : 0;
    for (let i = 0; i < escort; i++) spawnEnemy(Math.min(G.wave / 2, 6));
    bigMsg(`المرحلة ${G.wave}: ضرب أهداف أرضية — ${count} أهداف`);
    addFeed('اتبع السهم البرتقالي للوصول للأهداف');
  } else {
    const k = (G.wave + 1) / 2, n = Math.min(1 + k, 6);
    for (let i = 0; i < n; i++) spawnEnemy(Math.min(k - 1, 6));
    bigMsg(`المرحلة ${G.wave}: قتال جوي — ${n} طائرات`);
  }
}
function clearAll() {
  enemies.forEach(e => scene.remove(e.obj)); enemies.length = 0;
  grounds.forEach(t => scene.remove(t.obj)); grounds.length = 0;
  wrecks.forEach(w => scene.remove(w)); wrecks.length = 0;
  missiles.forEach(m => scene.remove(m.mesh)); missiles.length = 0;
  flares.forEach(f => scene.remove(f.mesh)); flares.length = 0;
  bullets.forEach(b => { b.alive = false; b.mesh.visible = false; });
  fx.forEach(e => { e.alive = false; e.mesh.visible = false; });
}
function buildPlayerPlane() {
  if (player.obj) scene.remove(player.obj);
  player.obj = makePlane(0xa9b1ba, COUNTRIES[G.country].colors, TYPES[G.type]);
  const sk = SKINS[G.skin];
  if (F16 && sk && sk.tint) {
    const col = sk.tint === 'flag' ? new THREE.Color(0xffffff).lerp(new THREE.Color(COUNTRIES[G.country].colors[0]), 0.4) : new THREE.Color(sk.tint);
    const made = new Map();
    player.obj.traverse(o => { if (o.isMesh && o.material && o.material.name === 'F-16-airframe') { if (!made.has(o.material)) { const c = o.material.clone(); c.color.copy(col); made.set(o.material, c); } o.material = made.get(o.material); } });
  }
}

/* الحظيرة: الطائرة واقفة وتنملي وقود */
function enterHangar() {
  clearAll();
  buildPlayerPlane();
  const T = TYPES[G.type];
  Object.assign(G, { mode: 'hangar', paused: false, wave: 0, kills: 0, maxHp: stat.maxHp, hp: stat.maxHp, gun: MAX_GUN, rockets: stat.rockets, maxMsl: stat.msl, msl: stat.msl, flr: 3, refillT: stat.refill, waveT: 0, wpn: +store.get('sf_wpn', 0) || 0, lockTarget: null, lockT: 0, locked: false, fuel: 0, cam: 0 });
  Object.assign(player, { yaw: 0, pitch: 0, roll: 0, yawRate: 0, pitchRate: 0, speed: 0, throttle: 0.6, alive: true });
  player.pos.set(0, RY + 2.6 * T.scale, HZ);
  player.obj.rotation.set(0, 0, 0);
  player.obj.position.copy(player.pos);
  player.obj.visible = true;
  feedList.length = 0;
  $('hgName').textContent = `${G.mission.free ? 'اللعب الحر' : 'المهمة ' + G.mission.n} — سلاح الجو ${COUNTRIES[G.country].name}`;
  $('hgTop').textContent = G.coins.toLocaleString('en') + ' عملة · ' + me();
  $('bTakeoff').disabled = true;
  ['hud', 'over', 'paused', 'svcUI', 'menu', 'brief', 'done', 'profile', 'forces', 'upgrade', 'splash'].forEach(id => show(id, false));
  show('hangarUI', true); show('hgTop', true);
}
function takeoff() {
  if (G.fuel < 100) return;
  show('hangarUI', false); show('hgTop', false); show('hud', true);
  updateWeaponUI();
  startIntro();
}
/* بعد الحظيرة: انميشن طيران سريع بدون تحكم، وبعدها يبدأ اللعب */
function startIntro() {
  const T = TYPES[G.type];
  G.mode = 'intro'; G.introT = 0;
  setSky(G.mission ? G.mission.sky : 'day');
  player.pos.set(0, RY + 320, -300);
  Object.assign(player, { yaw: 0, pitch: 0.04, roll: 0, speed: 160 * T.spd, throttle: 0.6 });
  player.obj.rotation.set(-player.pitch, 0, 0);
  player.obj.position.copy(player.pos);
  player.obj.visible = true;
  addFeed(`<b>${me()}</b> انطلق من القاعدة`);
  sfx.tone(90, 1.5, 'sawtooth', 0.05);
}
function introStep(dt) {
  G.introT += dt;
  player.roll = Math.sin(G.introT * 1.6) * 0.35;
  player.yaw = Math.sin(G.introT * 0.8) * 0.12;
  applyPlane(player, dt);
  player.obj.userData.glow.scale.setScalar(1.3);
  if (G.introT > 3.2) {
    G.mode = 'fly'; player.roll = 0;
    if (G.wave === 0) nextWave();
  }
}
/* بعد الصيانة: إقلاع تلقائي سريع من المدرج */
function startRunwayTakeoff() {
  show('svcUI', false);
  G.mode = 'takeoff'; G.toT = 0;
  player.speed = 0; player.pitch = 0; player.roll = 0;
  player.yaw = player.pos.z < 200 ? 0 : Math.PI; // يتجه للجهة الأطول من المدرج
  player.pos.x = 0;
  player.obj.rotation.set(0, player.yaw, 0);
  sfx.tone(90, 1.5, 'sawtooth', 0.05);
}
/* إقلاع يدوي: من الحظيرة أو بعد الصيانة */
function startGround() {
  G.mode = 'ground';
  show('svcUI', false);
  player.speed = 0; player.pitch = 0; player.roll = 0; player.yawRate = 0; player.pitchRate = 0;
  player.throttle = 0;
  player.obj.rotation.set(0, player.yaw, 0);
  addFeed(`${me()} على المدرج`);
}
function serviceStart() {
  G.mode = 'service'; G.svc = 0;
  G.svcHp0 = G.hp; G.svcFuel0 = G.fuel;
  $('bSvcGo').disabled = true;
  $('svcT').textContent = 'هبوط ناجح! جاري الصيانة...';
  show('svcUI', true);
  addFeed(`<b>${me()}</b> هبط في القاعدة للصيانة`);
  sfx.tone(520, 0.2, 'sine', 0.05);
}
/* الخسارة: انفجار، وبعد ثانيتين يرجع اللاعب بالسما مباشرة */
function playerDown(msg) {
  if (!player.alive) return;
  player.alive = false; G.mode = 'dead'; G.deadT = 2.2;
  player.obj.visible = false;
  explode(player.pos, true);
  if (msg) addFeed(msg);
  store.set('sf_coins', G.coins);
}
function respawn() {
  const T = TYPES[G.type];
  player.alive = true; G.mode = 'fly';
  player.pos.set(player.pos.x + rand(-300, 300), 450, player.pos.z + rand(-300, 300));
  Object.assign(player, { yaw: rand(-Math.PI, Math.PI), pitch: 0, roll: 0, yawRate: 0, pitchRate: 0, speed: 140 * T.spd, throttle: 0.6 });
  player.obj.rotation.set(0, player.yaw, 0);
  player.obj.position.copy(player.pos);
  player.obj.visible = true;
  camera.position.copy(player.pos).add(fwdOf(player.yaw, 0, V()).multiplyScalar(-30)).add(V().set(0, 8, 0));
  G.fireCd = 0; G.hp = G.maxHp; G.fuel = 100; G.gun = MAX_GUN; G.rockets = stat.rockets; G.msl = G.maxMsl; G.flr = 3; G.refillT = stat.refill; G.inv = 3;
  missiles.forEach(m => { if (m.target === player) m.target = null; });
  updateWeaponUI();
  addFeed(`<b>${me()}</b> رجع للسماء`);
  bigMsg('رجعت للسماء!');
}
function hurtPlayer(d) {
  if (!player.alive || G.mode !== 'fly' || G.inv > 0) return;
  G.hp -= d;
  $('flash').style.opacity = 0.35; setTimeout(() => $('flash').style.opacity = 0, 120);
  if (G.hp <= 0) { G.hp = 0; playerDown(`سقطت طائرة <b>${me()}</b>`); }
}
function killEnemy(e, how, byPlayer = true) {
  if (!e.alive) return;
  e.alive = false;
  explode(e.pos, true);
  scene.remove(e.obj);
  if (byPlayer) { G.kills++; if (!(G.mission && G.mission.free)) G.coins += 50; addFeed(`<b>${me()}</b> دمّر طائرة ${e.name}${how}`); }
}

/* ---------- القوائم ---------- */
const MENU_MODES = ['splash', 'menu', 'brief', 'profile', 'forces', 'upgrade', 'done'];
let toastT = 0;
function toast(t) { $('toast').textContent = t; $('toast').style.display = 'block'; toastT = 2.5; }
const LOCK_SVG = '<svg viewBox="0 0 24 24" aria-hidden="true"><rect x="5" y="10" width="14" height="11" rx="2" fill="#e8892c"/><path d="M8 10V7a4 4 0 0 1 8 0v3" fill="none" stroke="#e8892c" stroke-width="2.5"/></svg>';
function openScreen(id) {
  MENU_MODES.forEach(m => show(m, false));
  ['hud', 'hangarUI', 'hgTop', 'svcUI', 'paused', 'over'].forEach(x => show(x, false));
  show(id, true);
  G.mode = id === 'done' ? 'menu' : id;
}
function goMenu() {
  clearAll(); G.paused = false; G.mission = null;
  setSky('day');
  if (!player.obj) buildPlayerPlane();
  player.alive = true; player.obj.visible = true;
  openScreen('menu'); renderMenu();
}
function renderMenu() {
  $('mCoins').textContent = G.coins.toLocaleString('en') + ' عملة';
  const per = 8, from = G.page * per, list = MISSIONS.slice(from, from + per);
  $('mgrid').innerHTML = list.map((m, k) => {
    const i = from + k, locked = !m.free && m.n > G.unlocked, done = !m.free && m.n < G.unlocked;
    const next = !m.free && m.n === G.unlocked;
    const tag = locked ? 'مقفولة' : done ? '✓ مكتملة' : next ? 'التالية' : m.free ? 'تدريب' : '';
    return `<button class="mc${done ? ' done' : ''}${next ? ' next' : ''}${locked ? ' locked' : ''}" data-i="${i}" aria-label="${m.free ? m.name : 'المهمة ' + m.n}، ${tag}"><div class="th" style="background:${SKY_PRESETS[m.sky].thumb}">${locked ? LOCK_SVG : ''}<span class="tag">${tag}</span></div><div class="lb2">${m.free ? m.name : 'المهمة ' + m.n}</div></button>`;
  }).join('');
  $('mNext').style.display = from + per < MISSIONS.length ? '' : 'none';
  $('mPrev').style.display = G.page > 0 ? '' : 'none';
}
$('mgrid').addEventListener('click', e => {
  const b = e.target.closest('.mc'); if (!b) return;
  const m = MISSIONS[+b.dataset.i];
  if (!m.free && m.n > G.unlocked) { toast(`المهمة ${m.n} مقفولة، خلّص المهمة ${m.n - 1} أول`); return; }
  G.mission = m;
  $('briefT').textContent = m.free ? m.name : 'المهمة ' + m.n;
  $('briefTxt').textContent = m.brief;
  openScreen('brief');
});
$('mNext').addEventListener('click', () => { G.page++; renderMenu(); });
$('mPrev').addEventListener('click', () => { G.page = Math.max(0, G.page - 1); renderMenu(); });
$('bBriefX').addEventListener('click', () => { G.mission = null; openScreen('menu'); renderMenu(); });
$('bBriefGo').addEventListener('click', () => { if (!G.name) { toast('اكتب اسمك بالملف أول'); openProfile(); return; } enterHangar(); });
$('bDone').addEventListener('click', goMenu);
$('bShare').addEventListener('click', () => toast('المشاركة تجي مع النسخة النهائية من اللعبة'));
$('bTools').addEventListener('click', () => { openScreen('upgrade'); renderUpgrade(); });
$('bProfile').addEventListener('click', openProfile);

/* الملف */
function openProfile() { openScreen('profile'); renderProfile(); }
function renderProfile() {
  const c = COUNTRIES[G.country];
  $('avatar').innerHTML = avatarSvg(G.avatar);
  $('pFlag').innerHTML = c.svg; $('pRound').innerHTML = roundel(c); $('pCountry').textContent = 'سلاح الجو ' + c.name;
  $('nameIn').value = G.name;
  $('pCoins').textContent = G.coins.toLocaleString('en');
  $('pRank').textContent = RANKS[Math.min(G.unlocked - 1, RANKS.length - 1)];
  show('avPick', false);
}
$('nameIn').addEventListener('input', () => { G.name = $('nameIn').value.trim().slice(0, 16); store.set('sf_name', G.name); });
$('edAv').addEventListener('click', () => {
  $('avPick').innerHTML = AVATARS.map((a, i) => `<button data-i="${i}" class="${i === G.avatar ? 'on' : ''}" aria-label="صورة ${i + 1}">${avatarSvg(i)}</button>`).join('') + '<div style="grid-column:1/-1;text-align:center"><button class="ob" id="avOk" style="aspect-ratio:auto">OK</button></div>';
  show('avPick', true);
});
$('avPick').addEventListener('click', e => {
  if (e.target.id === 'avOk') { show('avPick', false); renderProfile(); return; }
  const b = e.target.closest('button[data-i]'); if (!b) return;
  G.avatar = +b.dataset.i; store.set('sf_av', G.avatar);
  [...$('avPick').querySelectorAll('button[data-i]')].forEach(x => x.classList.toggle('on', x === b));
});
$('edFlag').addEventListener('click', () => { G.forcesBack = 'profile'; openForces(); });
$('bLang').addEventListener('click', () => toast('اللعبة حالياً بالعربي فقط'));
$('pBack').addEventListener('click', () => { if (!G.name) { toast('اكتب اسمك أول'); $('nameIn').focus(); return; } saveAll(); goMenu(); });

/* القوات الجوية */
let pickC = 0, fPage = 0;
const F_PER = 12;
function openForces() { pickC = G.country; fPage = Math.floor(pickC / F_PER); openScreen('forces'); renderForces(); }
function renderForces() {
  const from = fPage * F_PER;
  $('fgrid').innerHTML = COUNTRIES.slice(from, from + F_PER).map((c, k) => { const i = from + k; return `<button class="fc${i === pickC ? ' on' : ''}" data-i="${i}" aria-pressed="${i === pickC}">${i === pickC ? '<span class="chk">✓</span>' : ''}${pennant(c)}<div class="nm">${c.name}</div></button>`; }).join('');
  $('fNext').style.display = from + F_PER < COUNTRIES.length ? '' : 'none';
  $('fPrev').style.display = fPage > 0 ? '' : 'none';
  $('fSel').textContent = pickC === G.country ? `سلاح الجو الحالي: ${COUNTRIES[pickC].name}` : `اخترت: ${COUNTRIES[pickC].name} — اضغط «انضم» للتأكيد`;
  $('bJoin').textContent = pickC === G.country ? '✓ منضم حالياً' : `انضم إلى ${COUNTRIES[pickC].name}`;
}
$('fNext').addEventListener('click', () => { fPage++; renderForces(); });
$('fPrev').addEventListener('click', () => { fPage = Math.max(0, fPage - 1); renderForces(); });
$('fgrid').addEventListener('click', e => { const b = e.target.closest('.fc'); if (!b) return; pickC = +b.dataset.i; renderForces(); });
$('bJoin').addEventListener('click', () => {
  const changed = G.country !== pickC;
  G.country = pickC; saveAll(); buildPlayerPlane();
  if (changed) toast('انضممت لسلاح الجو ' + COUNTRIES[G.country].name);
  if (G.forcesBack === 'profile') openProfile(); else goMenu();
});
$('fBack').addEventListener('click', () => { if (G.forcesBack === 'profile') openProfile(); else goMenu(); });

/* قاعدة التطوير */
let uTab = 'body';
function renderUpgrade() {
  $('uCoins').textContent = G.coins.toLocaleString('en') + ' عملة';
  $('tBody').classList.toggle('on', uTab === 'body'); $('tWpn').classList.toggle('on', uTab === 'wpn');
  const keys = uTab === 'body' ? ['armor', 'cool'] : ['power', 'reload', 'load'];
  $('upanel').innerHTML = keys.map(k => {
    const l = G.up[k], max = l >= UP_MAX, cost = upCost(l);
    return `<div class="urow">${UPS[k].name} <small style="color:#ccc">مستوى ${l}/${UP_MAX}</small><div class="ub"><span>${UPS[k].val(l)}</span><button data-k="${k}" ${max || G.coins < cost ? 'disabled' : ''}>${max ? 'الحد الأعلى' : 'تطوير ' + cost}</button></div></div>`;
  }).join('');
  const jet = (c, i) => `<svg class="j" viewBox="0 0 100 100" aria-hidden="true"><path d="${JET}" fill="${c}" stroke="#111" stroke-width="2" transform="rotate(-90 50 50)"/></svg>`;
  const flagCol = hex(COUNTRIES[G.country].colors[0]);
  const sel = G.previewSkin === undefined ? G.skin : G.previewSkin, sk = SKINS[sel];
  $('skins').innerHTML = SKINS.map((k, i) => { const own = G.skins.includes(i), st = i === G.skin ? '<span class="st2 use">✓ مستخدمة</span>' : own ? '<span class="st2 own">مملوكة</span>' : `<span class="st2 buy">${k.price} عملة</span>`;
    return `<button data-i="${i}" class="${i === sel ? 'on' : ''}" aria-pressed="${i === sel}">${own ? '' : `<span class="lk2">${LOCK_SVG}</span>`}${jet(i === 0 ? '#3a3f46' : i === 1 ? '#6f7d4e' : flagCol, i)}<div class="lb2">${k.name}${st}</div></button>`; }).join('');
  $('bSkin').textContent = sel === G.skin ? '✓ مستخدمة' : G.skins.includes(sel) ? 'استخدم هذا الشكل' : (G.coins < sk.price ? `تحتاج ${sk.price} عملة` : `شراء بـ ${sk.price} عملة`);
  $('bSkin').disabled = sel === G.skin || (!G.skins.includes(sel) && G.coins < sk.price);
}
$('tBody').addEventListener('click', () => { uTab = 'body'; renderUpgrade(); });
$('tWpn').addEventListener('click', () => { uTab = 'wpn'; renderUpgrade(); });
$('upanel').addEventListener('click', e => {
  const b = e.target.closest('button[data-k]'); if (!b) return;
  const k = b.dataset.k, cost = upCost(G.up[k]);
  if (G.coins < cost || G.up[k] >= UP_MAX) return;
  G.coins -= cost; G.up[k]++; saveAll(); sfx.tone(880, 0.15, 'sine', 0.06);
  toast(`تم تطوير ${UPS[k].name}`); renderUpgrade();
});
$('skins').addEventListener('click', e => {
  const b = e.target.closest('button[data-i]'); if (!b) return;
  G.previewSkin = +b.dataset.i;
  const keep = G.skin; G.skin = G.previewSkin; buildPlayerPlane(); G.skin = keep;
  renderUpgrade();
});
$('bSkin').addEventListener('click', () => {
  const i = G.previewSkin; if (i === undefined) return;
  if (!G.skins.includes(i)) { if (G.coins < SKINS[i].price) return; G.coins -= SKINS[i].price; G.skins.push(i); toast('اشتريت الشكل ' + SKINS[i].name); }
  G.skin = i; G.previewSkin = undefined; saveAll(); buildPlayerPlane(); renderUpgrade();
});
$('uBack').addEventListener('click', () => { G.previewSkin = undefined; buildPlayerPlane(); goMenu(); });

/* شاشة البداية */
$('bGo').addEventListener('click', () => {
  sfx.init(); goFull();
  if (!store.get('sf_name', '')) { G.forcesBack = 'profile'; openForces(); toast('اختر سلاح الجو اللي تنضم له'); }
  else goMenu();
});
$('bTakeoff').addEventListener('click', takeoff);
function goFull() {
  try {
    const d = document.documentElement, f = d.requestFullscreen || d.webkitRequestFullscreen;
    const r = f && f.call(d);
    if (r && r.catch) r.catch(() => addFeed('المتصفح رفض الشاشة الكاملة، افتح الرابط بمتصفح Chrome'));
  } catch (e) {}
  try { screen.orientation.lock('landscape').catch(() => {}); } catch (e) {}
}
$('bFull').addEventListener('click', goFull);
$('bFull2').addEventListener('click', goFull);
$('bSvcGo').addEventListener('click', startRunwayTakeoff);
$('bBack').addEventListener('click', goMenu);
$('bQuit').addEventListener('click', () => { G.paused = false; show('paused', false); saveAll(); goMenu(); });

/* ---------- التحكم ---------- */
const input = { jx: 0, jy: 0, gun: false, keys: {} };
(() => {
  const joy = $('joy'), knob = $('knob');
  let id = null;
  const upd = e => {
    const r = joy.getBoundingClientRect(), R = r.width / 2 - 14;
    let dx = e.clientX - (r.left + r.width / 2), dy = e.clientY - (r.top + r.height / 2);
    const l = Math.hypot(dx, dy); if (l > R) { dx *= R / l; dy *= R / l; }
    knob.style.transform = `translate(${dx}px,${dy}px)`;
    input.jx = dx / R; input.jy = -dy / R;
  };
  joy.addEventListener('pointerdown', e => { id = e.pointerId; joy.setPointerCapture(id); upd(e); e.preventDefault(); });
  joy.addEventListener('pointermove', e => { if (e.pointerId === id) upd(e); });
  const end = e => { if (e.pointerId === id) { id = null; input.jx = input.jy = 0; knob.style.transform = ''; } };
  joy.addEventListener('pointerup', end); joy.addEventListener('pointercancel', end);

  const thr = $('thr'); let tid = null;
  const tupd = e => { const r = thr.getBoundingClientRect(); player.throttle = clamp(1 - (e.clientY - r.top) / r.height, 0, 1); };
  thr.addEventListener('pointerdown', e => { tid = e.pointerId; thr.setPointerCapture(tid); tupd(e); e.preventDefault(); });
  thr.addEventListener('pointermove', e => { if (e.pointerId === tid) tupd(e); });
  thr.addEventListener('pointerup', () => tid = null); thr.addEventListener('pointercancel', () => tid = null);

  const fire = $('bFire');
  fire.addEventListener('pointerdown', e => { input.gun = true; fire.classList.add('on'); fire.setPointerCapture(e.pointerId); e.preventDefault(); firePressed(); });
  const fend = () => { input.gun = false; fire.classList.remove('on'); };
  fire.addEventListener('pointerup', fend); fire.addEventListener('pointercancel', fend);
  $('bSw').addEventListener('pointerdown', e => { e.preventDefault(); switchWeapon(); });
  $('bFlr').addEventListener('pointerdown', e => { e.preventDefault(); playerFlare(); });
  $('bCam').addEventListener('pointerdown', e => { e.preventDefault(); nextCam(); });
  $('bPause').addEventListener('click', () => setPause(true));
  $('bResume').addEventListener('click', () => setPause(false));
  addEventListener('keydown', e => {
    if (e.target && e.target.tagName === 'INPUT') return;
    const first = !input.keys[e.code];
    input.keys[e.code] = true;
    if (G.mode !== 'fly') return;
    if (e.code === 'Space' && first) firePressed();
    if (e.code === 'KeyX' || e.code === 'Tab') { e.preventDefault(); switchWeapon(); }
    if (e.code === 'KeyF') playerFlare();
    if (e.code === 'KeyC') nextCam();
    if (e.code === 'KeyP' || e.code === 'Escape') setPause(!G.paused);
  });
  addEventListener('keyup', e => input.keys[e.code] = false);
  document.addEventListener('touchmove', e => { if (!e.target.closest('.card')) e.preventDefault(); }, { passive: false });
})();
function setPause(p) { if (!['fly', 'intro', 'takeoff', 'landing', 'service', 'dead'].includes(G.mode)) return; G.paused = p; show('paused', p); }

const WPN = ['رشاش', 'صواريخ عادية', 'صواريخ حرارية'];
const CAMS = ['خلف الطائرة', 'القمرة', 'النظر للخلف'];
function updateWeaponUI() {
  [...$('wchips').children].forEach(c => c.classList.toggle('on', +c.dataset.w === G.wpn));
  $('icGun').style.display = G.wpn === 0 ? '' : 'none';
  $('icMsl').style.display = G.wpn === 0 ? 'none' : '';
  $('ammoN').textContent = G.wpn === 0 ? G.gun : G.wpn === 1 ? G.rockets : G.msl;
}
function switchWeapon() { if (G.mode !== 'fly') return; G.wpn = (G.wpn + 1) % 3; store.set('sf_wpn', G.wpn); updateWeaponUI(); sfx.tone(500, 0.06, 'triangle', 0.05); }
function nextCam() { if (!['fly', 'takeoff', 'landing', 'service'].includes(G.mode)) return; G.cam = (G.cam + 1) % 3; bigMsg('الكاميرا: ' + CAMS[G.cam]); }
const wingPos = side => player.pos.clone().add(V().set(side * 6, -1.5, 0).applyQuaternion(player.obj.quaternion));
function firePressed() {
  if (G.mode !== 'fly' || !player.alive || G.fireCd > 0) return;
  const f = fwdOf(player.yaw, player.pitch, V());
  if (G.wpn === 1) {
    if (G.rockets <= 0) { addFeed('الصواريخ العادية خلصت، انتظر التجديد'); return; }
    [-1, 1].forEach(s => { if (G.rockets <= 0) return; G.rockets--; const m = spawnMissile(player, wingPos(s), f, null); m.rocket = true; m.speed = m.maxSpeed = 330; m.life = 4; });
    G.fireCd = 0.45; sfx.whoosh(0.7);
  } else if (G.wpn === 2) {
    if (G.msl <= 0) { addFeed('الصواريخ الحرارية خلصت، انتظر التجديد'); return; }
    G.msl--;
    spawnMissile(player, wingPos(Math.random() < 0.5 ? -1 : 1), f, G.locked ? G.lockTarget : null);
    G.fireCd = 0.8; sfx.whoosh(1);
    if (!G.locked) addFeed('الصاروخ انطلق بدون قفل');
  }
  updateWeaponUI();
}
function playerFlare() {
  if (G.mode !== 'fly' || G.flr <= 0) return;
  G.flr--;
  dropFlares(player);
  sfx.flare();
  if (G.flr === 0) addFeed('خلص الفلير، يتجدد إذا سقطت طائرتك');
}

/* ---------- الحلقة الرئيسية ---------- */
const lockBoxes = [];
for (let i = 0; i < 8; i++) { const d = document.createElement('div'); d.className = 'lk'; d.innerHTML = '<span></span><i></i>'; $('locks').appendChild(d); lockBoxes.push(d); }
const rctx = $('radar').getContext('2d');
const proj = V();
const _ab = new THREE.Vector3(), _ap = new THREE.Vector3();
function segDist(a, b, p) {
  _ab.subVectors(b, a); _ap.subVectors(p, a);
  const l2 = _ab.lengthSq(), k = l2 > 0 ? clamp(_ap.dot(_ab) / l2, 0, 1) : 0;
  return _ap.sub(_ab.multiplyScalar(k)).length();
}
function toScreen(p) { proj.copy(p).project(camera); return proj.z < 1 && proj.z > -1 ? { x: (proj.x + 1) / 2 * innerWidth, y: (1 - proj.y) / 2 * innerHeight } : null; }

function syncPlayerFromQuat() {
  const pf = tmpV3.set(0, 0, 1).applyQuaternion(player.obj.quaternion);
  player.pitch = Math.asin(clamp(pf.y, -1, 1));
  player.yaw = Math.atan2(pf.x, pf.z);
  return pf;
}

function flyPlayer(dt, now) {
  const k = input.keys, T = TYPES[G.type];
  let jx = input.jx, jy = input.jy;
  if (k.ArrowLeft || k.KeyA) jx = -1; if (k.ArrowRight || k.KeyD) jx = 1;
  if (k.ArrowUp || k.KeyW) jy = 1; if (k.ArrowDown || k.KeyS) jy = -1;
  if (k.KeyE) player.throttle = Math.min(1, player.throttle + dt * 0.6);
  if (k.KeyQ) player.throttle = Math.max(0, player.throttle - dt * 0.6);
  // منحنى حساسية: الحركات الصغيرة أنعم، والسحب للآخر يعطي أقصى لفة
  jx = Math.sign(jx) * Math.pow(Math.abs(jx), 1.4);
  jy = Math.sign(jy) * Math.pow(Math.abs(jy), 1.4);
  const bonus = 1.3 + (0.85 - 1.3) * player.throttle; // سرعة أقل = لفة أحدّ
  /* طيران سهل: يمين ويسار تلف مباشرة، فوق وتحت للارتفاع، والطائرة ترجع مستوية لحالها */
  const TURN = 75 * Math.PI / 180 * T.turn * bonus, PIT = 55 * Math.PI / 180 * T.turn;
  player.yawRate += (-jx * TURN - player.yawRate) * Math.min(1, dt * 9);
  player.pitchRate += (jy * PIT - player.pitchRate) * Math.min(1, dt * 9);
  player.yaw += player.yawRate * dt;
  player.pitch = clamp(player.pitch + player.pitchRate * dt, -1.1, 1.1);
  if (Math.abs(jy) < 0.1) player.pitch *= 1 - Math.min(1, dt * 0.7);
  player.roll += (jx * 0.9 - player.roll) * Math.min(1, dt * 7);
  player.obj.rotation.set(-player.pitch, player.yaw, player.roll);
  if (G.inv > 0) { G.inv -= dt; player.obj.visible = G.cam !== 1 && Math.floor(G.inv * 8) % 2 === 0; }
  /* الوقود ينقص وقت الطيران، وإذا خلص المحرك يطفى */
  G.fuel = Math.max(0, G.fuel - dt * (0.15 + player.throttle * 0.3));
  if (G.fuel <= 0) {
    player.throttle = 0;
    player.pitch = Math.max(-0.9, player.pitch - 0.12 * dt);
    if (!G.outWarned) { G.outWarned = true; addFeed('خلص الوقود! المحرك طفى'); }
  } else G.outWarned = false;
  const pf = fwdOf(player.yaw, player.pitch, V());
  const minS = 60 * T.spd, maxS = 205 * T.spd;
  const targetS = G.fuel > 0 ? minS + player.throttle * (maxS - minS) : minS * 0.75;
  player.speed += (targetS - player.speed) * Math.min(1, dt * 1.5);
  player.vel.copy(pf).multiplyScalar(player.speed);
  player.pos.addScaledVector(player.vel, dt);
  player.obj.position.copy(player.pos);
  player.obj.userData.glow.scale.setScalar(0.8 + player.throttle * 0.6);

  /* الهبوط التلقائي: انزل منخفض فوق المدرج والطائرة تهبط بسلاسة */
  const nearRunway = Math.abs(player.pos.x) < RW / 2 + 40 && player.pos.z > -400 && player.pos.z < RZ1 - 80;
  if (nearRunway && player.pos.y < RY + 55) { startLanding(); return; }
  if (inBase(player.pos.x, player.pos.z)) { if (player.pos.y < RY + 4) player.pos.y = RY + 4; }
  else if (player.pos.y < groundH(player.pos.x, player.pos.z) + 5 * T.scale) {
    playerDown(`انفجار طائرة <b>${me()}</b> بسبب اصطدامها بالأرض`); return;
  }
  if (player.pos.y > 1500) player.pos.y = 1500;

  /* الرشاش: ذخيرة محدودة */
  player.gunCd -= dt;
  if ((input.gun || k.Space) && G.wpn === 0 && player.gunCd <= 0) {
    if (G.gun > 0) {
      G.gun--;
      const side = V().set(Math.cos(player.yaw), 0, -Math.sin(player.yaw)).multiplyScalar(Math.random() < 0.5 ? 2.2 : -2.2);
      spawnBullet('p', player.pos.clone().addScaledVector(pf, 9).add(side), pf.clone().add(V().set(rand(-0.012, 0.012), rand(-0.012, 0.012), rand(-0.012, 0.012))).normalize(), 750 + player.speed);
      player.gunCd = stat.gunCd;
      sfx.gun();
      if (G.gun % 10 === 0) updateWeaponUI();
    } else if (player.gunCd < -1) { addFeed('الرصاص خلص، انتظر التجديد'); player.gunCd = 0; }
  }
  /* تجديد الذخيرة كل 30 ثانية */
  G.fireCd = Math.max(0, G.fireCd - dt);
  G.refillT -= dt;
  if (G.refillT <= 0) {
    G.refillT = stat.refill;
    G.gun = MAX_GUN; G.rockets = stat.rockets; G.msl = G.maxMsl;
    addFeed('تم تجديد الذخيرة: رصاص، صواريخ عادية، صواريخ حرارية');
    updateWeaponUI(); sfx.tone(880, 0.2, 'sine', 0.06);
  }
  /* القفل: مخروط 35 درجة ومدى 1000 */
  let best = null, bestDot = 0.954;
  enemies.concat(grounds).forEach(e => { if (!e.alive) return; const d = tmpV.copy(e.pos).sub(player.pos); const l = d.length(); if (l > (e.isGround ? 1300 : 1000)) return; const dot = d.divideScalar(l).dot(pf); if (dot > bestDot) { bestDot = dot; best = e; } });
  if (best && best === G.lockTarget) G.lockT += dt; else { G.lockTarget = best; G.lockT = 0; }
  const wasLocked = G.locked;
  G.locked = !!best && G.lockT > 1.2;
  if (G.locked && (!wasLocked || now - G.lastLockBeep > 600)) { sfx.tone(1050, 0.08, 'sine', 0.06); G.lastLockBeep = now; }
}

/* الإقلاع التلقائي: يطلع من الحظيرة، يركض على المدرج، ويرتفع */
function takeoffStep(dt) {
  const T = TYPES[G.type];
  G.toT += dt;
  player.speed = Math.min(player.speed + 55 * dt, 155 * T.spd);
  if (G.toT > 2.4) player.pitch = Math.min(player.pitch + 0.3 * dt, 0.3);
  const pf = fwdOf(player.yaw, player.pitch, V());
  player.vel.copy(pf).multiplyScalar(player.speed);
  player.pos.addScaledVector(player.vel, dt);
  const floor = RY + 2.6 * T.scale;
  if (player.pos.y < floor) player.pos.y = floor;
  player.obj.position.copy(player.pos);
  player.obj.rotation.set(-player.pitch, player.yaw, 0);
  player.obj.userData.glow.scale.setScalar(1.4);
  if (Math.random() < 0.5 && player.pos.y < floor + 2) spawnFx(tmpV.copy(player.pos).add(V().set(rand(-2, 2), -2, -8)), 1.5, 0x9a9a9a, 0.6, 2);
  if (player.pos.y > RY + 140) {
    G.mode = 'fly';
    player.throttle = 0.6;
    addFeed(`<b>${me()}</b> أقلع من القاعدة`);
    if (G.wave === 0) nextWave();
  }
}

function startLanding() {
  G.mode = 'landing';
  G.land = { t: 0, y0: player.pos.y, x0: player.pos.x, yaw0: player.yaw, yaw1: Math.cos(player.yaw) >= 0 ? 0 : Math.PI, pitch0: player.pitch, roll0: player.roll };
  addFeed(`<b>${me()}</b> يهبط في القاعدة`);
  sfx.tone(300, 0.3, 'sine', 0.05);
}
/* هبوط سلس: تنعدل الطائرة على خط المدرج، تنزل بهدوء، وتوقف */
function landingStep(dt) {
  const L = G.land, T = TYPES[G.type], floor = RY + 2.6 * T.scale;
  L.t += dt;
  const k = Math.min(1, L.t / 2.4), e = k * k * (3 - 2 * k);
  player.yaw = L.yaw0 + wrapA(L.yaw1 - L.yaw0) * e;
  player.pos.y = L.y0 + (floor - L.y0) * e;
  player.pos.x = L.x0 * (1 - e);
  player.pitch = L.pitch0 * (1 - e) + 0.06 * Math.sin(k * Math.PI);
  player.roll = L.roll0 * (1 - e);
  if (k < 1) player.speed += (95 - player.speed) * Math.min(1, dt * 1.5);
  else player.speed = Math.max(0, player.speed - 42 * dt);
  const dir = Math.cos(L.yaw1) > 0 ? 1 : -1;
  player.pos.z += dir * player.speed * dt;
  if ((dir > 0 && player.pos.z > RZ1 - 60) || (dir < 0 && player.pos.z < -390)) player.speed = Math.max(0, player.speed - 160 * dt);
  player.pos.z = clamp(player.pos.z, -405, RZ1 - 25);
  player.vel.set(0, 0, dir * player.speed);
  player.obj.position.copy(player.pos);
  player.obj.rotation.set(-player.pitch, player.yaw, player.roll);
  player.obj.userData.glow.scale.setScalar(0.6);
  if (k >= 1 && player.speed > 20 && Math.random() < 0.4) spawnFx(tmpV.copy(player.pos).add(V().set(rand(-2, 2), -2, -dir * 6)), 1.4, 0x9a9a9a, 0.5, 2);
  if (k >= 1 && player.speed <= 0) serviceStart();
}
/* الصيانة: إصلاح + وقود + ذخيرة */
function serviceStep(dt) {
  if (G.svc >= 1) return;
  G.svc = Math.min(1, G.svc + dt / 4);
  G.hp = G.svcHp0 + (G.maxHp - G.svcHp0) * G.svc;
  G.fuel = G.svcFuel0 + (100 - G.svcFuel0) * G.svc;
  $('svHp').style.width = (G.hp / G.maxHp * 100) + '%';
  $('svFuel').style.width = G.fuel + '%';
  $('svAmmo').style.width = (G.svc * 100) + '%';
  if (G.svc >= 1) {
    G.gun = MAX_GUN; G.rockets = stat.rockets; G.msl = G.maxMsl; G.refillT = stat.refill;
    updateWeaponUI();
    $('svcT').textContent = 'الطائرة جاهزة للإقلاع';
    $('bSvcGo').disabled = false;
    sfx.tone(700, 0.15, 'sine', 0.05);
  }
}
/* الإقلاع اليدوي: ارفع السرعة واسحب الجويستك لفوق */
function groundStep(dt) {
  const T = TYPES[G.type], k = input.keys;
  let jx = input.jx, jy = input.jy;
  if (k.ArrowLeft || k.KeyA) jx = -1; if (k.ArrowRight || k.KeyD) jx = 1;
  if (k.ArrowUp || k.KeyW) jy = 1;
  if (k.KeyE) player.throttle = Math.min(1, player.throttle + dt * 0.6);
  if (k.KeyQ) player.throttle = Math.max(0, player.throttle - dt * 0.6);
  const liftS = 110 * T.spd;
  player.speed += (player.throttle * 190 * T.spd - player.speed) * Math.min(1, dt * 0.5);
  player.yaw -= jx * 0.25 * dt * Math.min(1, player.speed / 40);
  const floor = RY + 2.6 * T.scale;
  if (player.speed >= liftS && jy > 0.3) player.pitch = Math.min(player.pitch + 0.5 * dt, 0.3);
  else if (player.pos.y <= floor + 0.01) player.pitch = 0;
  const pf = fwdOf(player.yaw, player.pitch, V());
  player.vel.copy(pf).multiplyScalar(player.speed);
  player.pos.addScaledVector(player.vel, dt);
  if (player.pos.y < floor) player.pos.y = floor;
  player.obj.position.copy(player.pos);
  player.obj.rotation.set(-player.pitch, player.yaw, 0);
  player.obj.userData.glow.scale.setScalar(0.8 + player.throttle * 0.6);
  if (player.pos.y <= floor + 0.01 && !inBase(player.pos.x, player.pos.z)) {
    G.mode = 'fly'; G.hp = 0; playerDown(`انفجار طائرة <b>${me()}</b> بعد ما طلعت من المدرج`); return;
  }
  if (player.pos.y > floor + 6) {
    G.mode = 'fly';
    addFeed(`<b>${me()}</b> أقلع من القاعدة`);
    if (G.wave === 0) nextWave();
  }
}

let last = performance.now();
function tick(now) {
  requestAnimationFrame(tick);
  let dt = Math.min(0.05, (now - last) / 1000); last = now;
  if (G.paused) { sfx.setEngine(0, 0); renderer.render(scene, camera); return; }
  if (G.mode === 'dead') { G.deadT -= dt; if (G.deadT <= 0) respawn(); }

  if (toastT > 0) { toastT -= dt; if (toastT <= 0) show('toast', false); }
  /* معاينة الطائرة خلف القوائم */
  if (MENU_MODES.includes(G.mode)) {
    G.orbit += dt * 0.25;
    if (player.obj) {
      player.pos.set(0, 650, 0);
      player.obj.position.copy(player.pos);
      player.obj.rotation.set(0.05, 0.6, Math.sin(G.orbit * 1.3) * 0.12);
      player.obj.visible = true;
      if (player.obj.userData.setGear) player.obj.userData.setGear(false);
      player.obj.userData.glow && player.obj.userData.glow.scale.setScalar(1);
    }
    const right = G.mode === 'profile' || G.mode === 'upgrade';
    camera.position.set(Math.sin(G.orbit) * 30, 660, Math.cos(G.orbit) * 30);
    camera.up.set(0, 1, 0);
    const look = tmpV2.set(0, 650, 0);
    if (right) { const rv = V().subVectors(look, camera.position).cross(UPW).normalize(); look.addScaledVector(rv, -9); }
    camera.lookAt(look);
    sky.position.copy(camera.position);
    sunSpr.position.copy(camera.position).add(V().set(1800, 2600, 1300));
    sfx.setEngine(0, 0);
    renderer.render(scene, camera);
    return;
  }
  /* الحظيرة */
  if (G.mode === 'hangar') {
    if (G.mode === 'hangar' && G.fuel < 100) {
      G.fuel = Math.min(100, G.fuel + dt * 32);
      $('fuel').style.width = G.fuel + '%';
      $('fuelTxt').textContent = G.fuel < 100 ? `جاري تزويد الوقود... ${Math.floor(G.fuel)}%` : 'الوقود ممتلئ، اضغط ابدأ';
      if (G.fuel >= 100) { $('bTakeoff').disabled = false; sfx.tone(700, 0.15, 'sine', 0.05); }
    }
    G.orbit += dt * 0.3;
    const c = player.obj ? player.pos : tmpV2.set(0, RY + 3, HZ);
    camera.position.set(c.x + Math.sin(G.orbit) * 18, c.y + 5, c.z + Math.cos(G.orbit) * 18);
    camera.up.set(0, 1, 0);
    camera.lookAt(c);
    if (player.obj && player.obj.userData.setGear) player.obj.userData.setGear(true);
    sfx.setEngine(0.05, 0.35);
    sky.position.copy(camera.position);
    renderer.render(scene, camera);
    return;
  }

  if (G.mode === 'intro') introStep(dt);
  else if (G.mode === 'takeoff') takeoffStep(dt);
  else if (G.mode === 'fly' && player.alive) flyPlayer(dt, now);
  else if (G.mode === 'landing') landingStep(dt);
  else if (G.mode === 'service') serviceStep(dt);
  else if (G.mode === 'ground') groundStep(dt);

  /* الأعداء + التصادم */
  enemies.forEach(e => e.alive && updateEnemy(e, dt));
  grounds.forEach(t => t.alive && updateGround(t, dt));
  for (let i = grounds.length - 1; i >= 0; i--) if (!grounds[i].alive) grounds.splice(i, 1);
  for (let i = 0; i < enemies.length; i++) {
    const a = enemies[i]; if (!a.alive) continue;
    if (a.pos.y < surfaceH(a.pos.x, a.pos.z) + 4) { addFeed(`انفجار طائرة ${a.name} بسبب اصطدامها بالأرض`); killEnemy(a, '', false); continue; }
    if (G.mode === 'fly' && player.alive && !(G.inv > 0) && a.pos.distanceTo(player.pos) < 13) {
      killEnemy(a, '', false); G.hp = 0;
      playerDown(`انفجار طائرة <b>${me()}</b> وطائرة ${a.name} بسبب التصادم`); continue;
    }
    for (let j = i + 1; j < enemies.length; j++) {
      const b = enemies[j];
      if (b.alive && a.pos.distanceTo(b.pos) < 13) { addFeed(`انفجار طائرتي ${a.name} و${b.name} بسبب التصادم`); killEnemy(a, '', false); killEnemy(b, '', false); break; }
    }
  }
  for (let i = enemies.length - 1; i >= 0; i--) if (!enemies[i].alive) enemies.splice(i, 1);
  if (G.mode === 'fly' && enemies.length === 0 && grounds.length === 0) {
    if (G.mission && !G.mission.free) { if (G.wave >= 1) missionComplete(); }
    else if (G.waveT <= 0) { G.waveT = 3; bigMsg('أحسنت! الموجة القادمة...'); }
    if (G.mode === 'fly') { G.waveT -= dt; if (G.waveT <= 0) nextWave(); }
  }

  /* الرصاص */
  bullets.forEach(b => {
    if (!b.alive) return;
    b.pos.addScaledVector(b.dir, b.speed * dt); b.mesh.position.copy(b.pos); b.life -= dt;
    if (b.life <= 0) { b.alive = false; b.mesh.visible = false; return; }
    if (b.owner === 'p') {
      for (const e of enemies) if (e.alive && e.pos.distanceToSquared(b.pos) < 100) {
        e.hp -= 9 * stat.dmg; b.alive = false; b.mesh.visible = false;
        spawnFx(b.pos, 2, 0xffd080, 0.25, 2);
        if (e.hp <= 0) killEnemy(e, '');
        else if (Math.random() < 0.3 && e.state === 'chase') { e.state = 'evade'; e.t = rand(1.5, 3); e.jy = 0; }
        break;
      }
      if (b.alive) for (const t of grounds) if (t.alive && t.pos.distanceToSquared(b.pos) < 90) {
        b.alive = false; b.mesh.visible = false; damageGround(t, 8 * stat.dmg, ''); break;
      }
    } else if (player.alive && player.pos.distanceToSquared(b.pos) < 64) {
      b.alive = false; b.mesh.visible = false; hurtPlayer(3);
    }
  });

  /* الصواريخ */
  let threat = false;
  missiles.forEach(m => {
    if (!m.alive) return;
    m.life -= dt;
    m.speed = Math.min(m.maxSpeed, m.speed + 160 * dt);
    const t = m.target;
    const tAlive = t && (t.isFlare ? t.life > 0 : t.alive);
    if (tAlive && !m.lost) {
      // الباحث الحراري يشوف بس قدّامه: إذا الهدف طلع برا زاوية الرؤية يضيع الصاروخ
      if (tmpV3.copy(t.pos).sub(m.pos).normalize().dot(m.dir) < 0.3) {
        m.lost = true;
        if (t === player) addFeed('تفاديت الصاروخ!');
      } else {
        // توجيه بالتقدّم: يصوّب على المكان اللي راح يوصله الهدف، مو مكانه الحالي
        const dist = m.pos.distanceTo(t.pos), tgo = Math.min(3, dist / Math.max(120, m.speed));
        const aim = tmpV.copy(t.pos); if (t.vel) aim.addScaledVector(t.vel, tgo);
        const want = aim.sub(m.pos).normalize(), ang = m.dir.angleTo(want);
        if (ang > 1e-4) {
          const axis = tmpV2.crossVectors(m.dir, want);
          if (axis.lengthSq() > 1e-10) m.dir.applyAxisAngle(axis.normalize(), Math.min(ang, m.turn * dt)).normalize();
        }
      }
    }
    if (m.rocket && m.alive) for (const e of enemies) if (e.alive && e.pos.distanceTo(m.pos) < 14) {
      m.alive = false; explode(m.pos, false); e.hp -= 60 * stat.dmg;
      if (e.hp <= 0) killEnemy(e, ' بصاروخ'); break;
    }
    if (m.rocket && m.alive) for (const t of grounds) if (t.alive && t.pos.distanceTo(m.pos) < 16) {
      m.alive = false; explode(m.pos, false); damageGround(t, 70 * stat.dmg, ' بصاروخ'); break;
    }
    m.prev.copy(m.pos);
    m.pos.addScaledVector(m.dir, m.speed * dt);
    // فحص الإصابة على طول المسار حتى الصاروخ السريع ما يعبر الهدف بدون ما ينفجر
    if (m.alive && tAlive && !m.lost && segDist(m.prev, m.pos, t.pos) < 9) {
      m.alive = false; explode(t.pos, false);
      if (t === player) { hurtPlayer(45); addFeed('أصابك صاروخ!'); }
      else if (t.isEnemy) killEnemy(t, ' بصاروخ حراري');
      else if (t.isGround) damageGround(t, 100, ' بصاروخ حراري');
      else t.life = 0;
    }
    if (m.alive && tAlive && !m.lost && t === player) threat = true;
    m.mesh.position.copy(m.pos); m.mesh.lookAt(tmpV.copy(m.pos).add(m.dir));
    m.smokeT -= dt;
    if (m.smokeT <= 0) { spawnFx(m.pos, 1.6, 0xdddddd, 0.9, 2.5); m.smokeT = 0.04; }
    if (m.life <= 0 || m.pos.y < surfaceH(m.pos.x, m.pos.z)) { if (m.alive) explode(m.pos, false); m.alive = false; }
    if (!m.alive) scene.remove(m.mesh);
  });
  for (let i = missiles.length - 1; i >= 0; i--) if (!missiles[i].alive) missiles.splice(i, 1);

  /* الفلير */
  flares.forEach(f => {
    f.life -= dt; f.vel.y -= 25 * dt; f.pos.addScaledVector(f.vel, dt); f.mesh.position.copy(f.pos);
    if (Math.random() < 0.5) spawnFx(f.pos, 1, 0xffb050, 0.4, 1.5);
    if (f.life <= 0) { f.alive = false; scene.remove(f.mesh); }
  });
  for (let i = flares.length - 1; i >= 0; i--) if (flares[i].life <= 0) flares.splice(i, 1);

  /* المؤثرات */
  fx.forEach(e => {
    if (!e.alive) return;
    e.t += dt; const k = e.t / e.life;
    if (k >= 1) { e.alive = false; e.mesh.visible = false; return; }
    e.mesh.scale.setScalar(2.2 * e.size * (1 + k * e.grow));
    e.mesh.material.opacity = 1 - k;
  });

  /* الأرض والغيوم تلحق اللاعب */
  ground.position.set(Math.round(player.pos.x / 800) * 800, 0, Math.round(player.pos.z / 800) * 800);
  clouds.forEach(c => {
    if (Math.abs(c.position.x - player.pos.x) > 2600) c.position.x = player.pos.x - Math.sign(c.position.x - player.pos.x) * 2500;
    if (Math.abs(c.position.z - player.pos.z) > 2600) c.position.z = player.pos.z - Math.sign(c.position.z - player.pos.z) * 2500;
  });

  /* الكاميرا: خلف الطائرة، القمرة، النظر للخلف */
  const cf = fwdOf(player.yaw, player.pitch, V());
  const pq = player.obj.quaternion;
  const pUp = V().set(0, 1, 0).applyQuaternion(pq);
  if (!(G.inv > 0)) player.obj.visible = player.alive && G.cam !== 1;
  if (G.mode === 'intro') {
    // الكاميرا تلف من جنب الطائرة لحد ما توصل خلفها
    const k = Math.min(1, G.introT / 3), a = Math.PI / 2 + k * Math.PI / 2;
    const off = V().set(Math.sin(a) * 34, 7 - k * 1, Math.cos(a) * 34).applyAxisAngle(UPW, player.yaw);
    camera.position.copy(player.pos).add(off);
    camera.up.set(0, 1, 0);
    camera.lookAt(tmpV2.copy(player.pos).addScaledVector(cf, 20 * k));
  } else if (G.cam === 1) {
    camera.position.copy(player.pos).add(V().set(0, 2.9, 5.4).multiplyScalar(player.obj.scale.x).applyQuaternion(pq));
    camera.up.copy(pUp);
    camera.lookAt(tmpV2.copy(player.pos).addScaledVector(cf, 100));
  } else if (G.cam === 2) {
    camera.position.lerp(V().set(0, 5, 18).applyQuaternion(pq).add(player.pos), 1 - Math.exp(-dt * 8));
    camera.up.set(0, 1, 0);
    camera.lookAt(tmpV2.copy(player.pos).addScaledVector(cf, -80));
  } else {
    camera.position.lerp(V().set(0, 8, -30).applyQuaternion(pq).add(player.pos), 1 - Math.exp(-dt * 6));
    camera.up.set(0, 1, 0).lerp(pUp, 0.35).normalize();
    camera.lookAt(tmpV2.copy(player.pos).addScaledVector(cf, 60));
  }

  sfx.setEngine(player.throttle, !player.alive || G.mode === 'donewait' ? 0 : G.mode === 'service' ? 0.25 : G.mode === 'landing' ? 0.5 : 1);
  if (player.obj.userData.setGear) player.obj.userData.setGear(G.mode === 'service' || G.mode === 'landing' || (G.mode === 'takeoff' && player.pos.y < RY + 25));
  sky.position.copy(camera.position);
  sunSpr.position.copy(camera.position).add(V().set(1800, 2600, 1300));
  renderer.render(scene, camera);
  drawHUD(dt, threat, cf);
}

function drawHUD(dt, threat, cf) {
  const hpP = G.hp / G.maxHp * 100;
  $('hp').style.width = hpP + '%';
  $('hp').style.background = hpP > 50 ? '#3aa7e0' : hpP > 25 ? '#ff8a3d' : '#ff3a2a';
  $('coins').textContent = G.coins.toLocaleString('en');
  $('kills').textContent = 'قتلى ' + G.kills;
  $('wave').textContent = 'الموجة ' + G.wave;
  $('flrN').textContent = G.flr;
  $('ammoN').textContent = G.wpn === 0 ? G.gun : G.wpn === 1 ? G.rockets : G.msl;
  $('refill').textContent = 'تجديد الذخيرة ' + Math.ceil(G.refillT) + 'ث';
  $('spd').textContent = 'السرعة ' + Math.round(player.speed * 4.5) + ' كم/س';
  $('fuelBar').style.width = G.fuel + '%';
  $('fuelBar').style.background = G.fuel > 25 ? '#e8c547' : '#ff3a2a';
  beacon.visible = Math.floor(performance.now() / 500) % 2 === 0;
  /* سهم الأهداف الأرضية */
  let nearT = null, nd = 1e9;
  grounds.forEach(t => { if (!t.alive) return; const dd = t.pos.distanceTo(player.pos); if (dd < nd) { nd = dd; nearT = t; } });
  $('tgtInd').style.display = nearT ? 'block' : 'none';
  if (nearT) {
    const trel = wrapA(Math.atan2(nearT.pos.x - player.pos.x, nearT.pos.z - player.pos.z) - player.yaw);
    $('tgtArrow').style.transform = `rotate(${-trel * 180 / Math.PI}deg)`;
    $('tgtTxt').textContent = 'الهدف ' + (nd * 5 / 1000).toFixed(1) + 'كم · ' + grounds.filter(t => t.alive).length;
  }
  /* سهم القاعدة */
  const bdx = 0 - player.pos.x, bdz = 150 - player.pos.z;
  const bDist = Math.hypot(bdx, bdz);
  const rel = wrapA(Math.atan2(bdx, bdz) - player.yaw);
  $('baseArrow').style.transform = `rotate(${-rel * 180 / Math.PI}deg)`;
  $('baseTxt').textContent = 'القاعدة ' + (bDist * 5 / 1000).toFixed(1) + 'كم';
  /* تلميحات */
  let hint = '';
  if (G.mode === 'ground') hint = player.speed < 110 * TYPES[G.type].spd ? 'ارفع عتلة السرعة للآخر' : 'اسحب الجويستك لفوق حتى تقلع';
  else if (G.mode === 'fly' && bDist < 1400 && player.pos.y < RY + 260 && (G.fuel < 40 || G.hp < G.maxHp * 0.5)) hint = 'للهبوط: انزل منخفض فوق المدرج، والطائرة تهبط لحالها';
  else if (G.mode === 'fly' && G.fuel < 25 && G.fuel > 0) hint = 'الوقود قليل! ارجع للقاعدة';
  $('hint').textContent = hint; $('hint').style.display = hint ? 'block' : 'none';
  $('thrFill').style.height = player.throttle * 100 + '%';
  $('thrKnob').style.bottom = player.throttle * 100 + '%';
  feedList.forEach(f => f.t -= dt);
  while (feedList.length && feedList[feedList.length - 1].t <= 0) feedList.pop();
  $('feed').innerHTML = feedList.map(f => `<div>${f.html}</div>`).join('');
  if (bigT > 0) { bigT -= dt; if (bigT <= 0) $('big').style.opacity = 0; }
  G.warnT += dt;
  const altG = player.pos.y - surfaceH(player.pos.x, player.pos.z);
  const low = G.mode === 'fly' && player.alive && altG < 70 && !inBase(player.pos.x, player.pos.z) && Math.hypot(player.pos.x, player.pos.z - 150) > 700;
  const high = G.mode === 'fly' && player.alive && player.pos.y > 1300;
  const wtxt = threat ? '⚠ صاروخ موجّه عليك! اضغط فلير' : low ? '⚠ ارتفاع منخفض! ارفع الطائرة' : high ? '⚠ ارتفاع عالي! انزل شوي' : '';
  $('warn').textContent = wtxt;
  $('warn').style.display = wtxt && Math.floor(G.warnT * 3) % 2 === 0 ? 'block' : 'none';
  sfx.alarms(dt, threat, low, high);

  const c = toScreen(tmpV.copy(player.pos).addScaledVector(cf, 400));
  if (c) { $('cross').style.left = c.x + 'px'; $('cross').style.top = c.y + 'px'; }

  let i = 0;
  enemies.concat(grounds).forEach(e => {
    if (!e.alive || i >= lockBoxes.length) return;
    const s = toScreen(e.pos); if (!s) return;
    const b = lockBoxes[i++];
    b.style.display = 'block'; b.style.left = s.x + 'px'; b.style.top = s.y + 'px';
    const isT = e === G.lockTarget, dist = e.pos.distanceTo(player.pos);
    const far = dist > (e.isGround ? 1300 : 1000);
    b.className = 'lk' + (isT ? (G.locked ? ' lock' : ' cand') : far ? ' far' : e.isGround ? ' gnd' : '');
    b.firstChild.textContent = isT && G.locked ? 'تم القفل ☠ ' + e.name : far ? '' : (e.isGround ? e.name : 'Enemy') + ` ${Math.round(dist)}م`;
    b.lastChild.style.width = far ? '0' : Math.max(0, e.hp / (e.maxHp || 100) * 100) + '%';
  });
  for (; i < lockBoxes.length; i++) lockBoxes[i].style.display = 'none';

  const R = 48, range = 1600;
  rctx.clearRect(0, 0, 96, 96);
  rctx.strokeStyle = 'rgba(63,191,95,.45)'; rctx.lineWidth = 1;
  rctx.beginPath(); rctx.arc(R, R, R * 0.5, 0, 7); rctx.moveTo(R, 2); rctx.lineTo(R, 94); rctx.moveTo(2, R); rctx.lineTo(94, R); rctx.stroke();
  const cy = Math.cos(player.yaw), sy = Math.sin(player.yaw);
  const blip = (p, col, r) => {
    const dx = p.x - player.pos.x, dz = p.z - player.pos.z;
    let x = -(dx * cy - dz * sy) / range * R, y = -(dx * sy + dz * cy) / range * R;
    const l = Math.hypot(x, y); if (l > R - 5) { x *= (R - 5) / l; y *= (R - 5) / l; }
    rctx.fillStyle = col; rctx.beginPath(); rctx.arc(R + x, R + y, r, 0, 7); rctx.fill();
  };
  enemies.forEach(e => e.alive && blip(e.pos, '#ff4a3a', 3.5));
  grounds.forEach(t => t.alive && blip(t.pos, '#ff9a3a', 3));
  missiles.forEach(m => m.alive && m.target === player && blip(m.pos, '#ffffff', 2));
  blip(tmpV.set(0, 0, 150), '#5ab0ff', 4.5);
  rctx.fillStyle = '#7dff9a'; rctx.beginPath(); rctx.moveTo(R, R - 6); rctx.lineTo(R - 4, R + 4); rctx.lineTo(R + 4, R + 4); rctx.fill();
}

/* البداية: المشهد خلف شاشة الدخول يعرض الحظيرة */
buildPlayerPlane();
loadModel();
if (/[?&]test\b/.test(location.search)) window.__sf = { G, player, enemies, grounds, missiles, flares, MISSIONS, COUNTRIES, stat, input, spawnMissile, spawnEnemy, RY, get SAM() { return SAM; }, get F16() { return F16; } };
player.pos.set(0, RY + 2.6, HZ);
player.obj.position.copy(player.pos);
requestAnimationFrame(tick);
}
</script>
</body>
</html>

