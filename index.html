import { useState, useRef, useEffect, useCallback } from "react";

const API_KEY_PLACEHOLDER = ""; // handled by proxy

export default function ObjectRemover() {
  const [image, setImage] = useState(null);
  const [tool, setTool] = useState("brush");
  const [brushSize, setBrushSize] = useState(22);
  const [isDrawing, setIsDrawing] = useState(false);
  const [processing, setProcessing] = useState(false);
  const [result, setResult] = useState(null);
  const [status, setStatus] = useState("");
  const [autoDetecting, setAutoDetecting] = useState(false);
  const [hasMask, setHasMask] = useState(false);
  const [mode, setMode] = useState("idle"); // idle | editor | result

  const displayCanvasRef = useRef(null);
  const maskCanvasRef = useRef(null); // offscreen mask
  const originalImageRef = useRef(null);
  const fileRef = useRef(null);
  const lastPos = useRef(null);
  const imgDims = useRef({ w: 0, h: 0 });
  const containerRef = useRef(null);

  const getScale = useCallback(() => {
    const canvas = displayCanvasRef.current;
    if (!canvas || !imgDims.current.w) return 1;
    return canvas.offsetWidth / imgDims.current.w;
  }, []);

  const toImageCoords = (e) => {
    const canvas = displayCanvasRef.current;
    const rect = canvas.getBoundingClientRect();
    const scaleX = imgDims.current.w / canvas.offsetWidth;
    const scaleY = imgDims.current.h / canvas.offsetHeight;
    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
    const clientY = e.touches ? e.touches[0].clientY : e.clientY;
    return {
      x: (clientX - rect.left) * scaleX,
      y: (clientY - rect.top) * scaleY,
    };
  };

  const redrawDisplay = useCallback(() => {
    const canvas = displayCanvasRef.current;
    const mask = maskCanvasRef.current;
    const img = originalImageRef.current;
    if (!canvas || !mask || !img) return;
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.drawImage(img, 0, 0);
    // overlay mask in red
    const mCtx = mask.getContext("2d");
    const mData = mCtx.getImageData(0, 0, mask.width, mask.height);
    const overlay = ctx.createImageData(canvas.width, canvas.height);
    for (let i = 0; i < mData.data.length; i += 4) {
      if (mData.data[i] > 128) {
        overlay.data[i] = 255;
        overlay.data[i + 1] = 60;
        overlay.data[i + 2] = 60;
        overlay.data[i + 3] = 140;
      }
    }
    ctx.putImageData(overlay, 0, 0);
  }, []);

  const drawOnMask = useCallback(
    (x, y, erase = false) => {
      const mask = maskCanvasRef.current;
      if (!mask) return;
      const ctx = mask.getContext("2d");
      ctx.globalCompositeOperation = erase ? "destination-out" : "source-over";
      ctx.fillStyle = "white";
      ctx.beginPath();
      ctx.arc(x, y, brushSize, 0, Math.PI * 2);
      ctx.fill();
      ctx.globalCompositeOperation = "source-over";
      redrawDisplay();
      setHasMask(true);
    },
    [brushSize, redrawDisplay]
  );

  const onPointerDown = (e) => {
    if (!image) return;
    setIsDrawing(true);
    const pos = toImageCoords(e);
    lastPos.current = pos;
    drawOnMask(pos.x, pos.y, tool === "eraser");
  };

  const onPointerMove = (e) => {
    if (!isDrawing || !image) return;
    const pos = toImageCoords(e);
    const mask = maskCanvasRef.current;
    const ctx = mask.getContext("2d");
    ctx.globalCompositeOperation = tool === "eraser" ? "destination-out" : "source-over";
    ctx.strokeStyle = "white";
    ctx.lineWidth = brushSize * 2;
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    ctx.beginPath();
    ctx.moveTo(lastPos.current.x, lastPos.current.y);
    ctx.lineTo(pos.x, pos.y);
    ctx.stroke();
    ctx.globalCompositeOperation = "source-over";
    lastPos.current = pos;
    redrawDisplay();
    setHasMask(true);
  };

  const onPointerUp = () => setIsDrawing(false);

  const clearMask = () => {
    const mask = maskCanvasRef.current;
    if (!mask) return;
    const ctx = mask.getContext("2d");
    ctx.clearRect(0, 0, mask.width, mask.height);
    redrawDisplay();
    setHasMask(false);
    setResult(null);
  };

  const loadImage = (file) => {
    if (!file || !file.type.startsWith("image/")) return;
    const url = URL.createObjectURL(file);
    const img = new Image();
    img.onload = () => {
      originalImageRef.current = img;
      imgDims.current = { w: img.naturalWidth, h: img.naturalHeight };

      const displayCanvas = displayCanvasRef.current;
      displayCanvas.width = img.naturalWidth;
      displayCanvas.height = img.naturalHeight;

      const maskCanvas = maskCanvasRef.current;
      maskCanvas.width = img.naturalWidth;
      maskCanvas.height = img.naturalHeight;

      const ctx = displayCanvas.getContext("2d");
      ctx.drawImage(img, 0, 0);

      setImage({ url, w: img.naturalWidth, h: img.naturalHeight });
      setResult(null);
      setHasMask(false);
      setMode("editor");
      setStatus("");
    };
    img.src = url;
  };

  const onDrop = (e) => {
    e.preventDefault();
    loadImage(e.dataTransfer.files[0]);
  };

  // ---- AUTO DETECT via Claude API ----
  const autoDetect = async (type) => {
    if (!image) return;
    setAutoDetecting(true);
    setStatus(`🔍 Auto-detecting ${type}...`);
    try {
      const canvas = displayCanvasRef.current;
      const base64 = canvas.toDataURL("image/jpeg", 0.85).split(",")[1];

      const prompt = `Look at this image carefully. Find any ${type === "watermark" ? "watermark or semi-transparent text overlay" : type === "logo" ? "logo, brand mark, or icon overlay" : "main foreground object or person"}. Return ONLY a JSON object (no markdown) with these fields: { "found": true/false, "items": [{ "label": "description", "x": 0-100, "y": 0-100, "w": 0-100, "h": 0-100 }] }. x, y, w, h are percentages of image dimensions. If multiple found, list all.`;

      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 500,
          messages: [
            {
              role: "user",
              content: [
                { type: "image", source: { type: "base64", media_type: "image/jpeg", data: base64 } },
                { type: "text", text: prompt },
              ],
            },
          ],
        }),
      });

      const data = await res.json();
      const rawText = data.content?.find((b) => b.type === "text")?.text || "";
      const clean = rawText.replace(/```json|```/g, "").trim();
      const parsed = JSON.parse(clean);

      if (!parsed.found || !parsed.items?.length) {
        setStatus(`⚠️ No ${type} detected in this image.`);
        setAutoDetecting(false);
        return;
      }

      const mask = maskCanvasRef.current;
      const ctx = mask.getContext("2d");
      ctx.fillStyle = "white";
      parsed.items.forEach(({ x, y, w, h, label }) => {
        const px = (x / 100) * imgDims.current.w;
        const py = (y / 100) * imgDims.current.h;
        const pw = (w / 100) * imgDims.current.w;
        const ph = (h / 100) * imgDims.current.h;
        // add some padding
        ctx.fillRect(px - pw * 0.05, py - ph * 0.05, pw * 1.1, ph * 1.1);
        setStatus(`✅ Detected: "${label}" — now click Remove!`);
      });

      redrawDisplay();
      setHasMask(true);
    } catch (err) {
      setStatus("❌ Detection failed. Try manual brush.");
    }
    setAutoDetecting(false);
  };

  // ---- INPAINTING ----
  const removeObject = async () => {
    if (!hasMask) { setStatus("⚠️ Paint over the object first!"); return; }
    setProcessing(true);
    setStatus("⚙️ Removing object...");

    await new Promise((r) => setTimeout(r, 50));

    const srcCanvas = displayCanvasRef.current;
    const mask = maskCanvasRef.current;
    const W = imgDims.current.w;
    const H = imgDims.current.h;

    // Get original image pixels
    const offscreen = document.createElement("canvas");
    offscreen.width = W;
    offscreen.height = H;
    const octx = offscreen.getContext("2d");
    octx.drawImage(originalImageRef.current, 0, 0);
    const imgData = octx.getImageData(0, 0, W, H);
    const pixels = imgData.data;

    // Get mask
    const mCtx = mask.getContext("2d");
    const mData = mCtx.getImageData(0, 0, W, H).data;
    const maskArr = new Uint8Array(W * H);
    for (let i = 0; i < W * H; i++) {
      maskArr[i] = mData[i * 4] > 100 ? 1 : 0;
    }

    // Inpainting: boundary-first fill with weighted avg
    const result = new Uint8ClampedArray(pixels);
    const filled = new Uint8Array(maskArr); // starts as copy
    const total = maskArr.reduce((a, b) => a + b, 0);

    // Multi-pass expanding fill
    for (let pass = 0; pass < 40; pass++) {
      let changed = false;
      const newFilled = new Uint8Array(filled);

      for (let y = 0; y < H; y++) {
        for (let x = 0; x < W; x++) {
          const idx = y * W + x;
          if (!filled[idx]) continue; // not masked
          if (!maskArr[idx]) continue; // original unmasked pixel, skip

          let r = 0, g = 0, b = 0, w = 0;
          const rad = Math.min(pass + 2, 12);

          for (let dy = -rad; dy <= rad; dy++) {
            for (let dx = -rad; dx <= rad; dx++) {
              const nx = x + dx, ny = y + dy;
              if (nx < 0 || nx >= W || ny < 0 || ny >= H) continue;
              const nIdx = ny * W + nx;
              if (filled[nIdx] && maskArr[nIdx]) continue; // skip still-masked
              const d = Math.sqrt(dx * dx + dy * dy) + 0.5;
              const wt = 1 / (d * d);
              r += result[nIdx * 4] * wt;
              g += result[nIdx * 4 + 1] * wt;
              b += result[nIdx * 4 + 2] * wt;
              w += wt;
            }
          }

          if (w > 0) {
            result[idx * 4] = r / w;
            result[idx * 4 + 1] = g / w;
            result[idx * 4 + 2] = b / w;
            result[idx * 4 + 3] = 255;
            newFilled[idx] = 0;
            changed = true;
          }
        }
      }

      for (let i = 0; i < filled.length; i++) filled[i] = newFilled[i];
      if (!changed) break;
    }

    // Smooth masked region edges
    for (let y = 1; y < H - 1; y++) {
      for (let x = 1; x < W - 1; x++) {
        const idx = y * W + x;
        if (!maskArr[idx]) continue;
        for (let c = 0; c < 3; c++) {
          const sum =
            result[(idx - W) * 4 + c] +
            result[(idx + W) * 4 + c] +
            result[(idx - 1) * 4 + c] +
            result[(idx + 1) * 4 + c] +
            result[idx * 4 + c] * 2;
          result[idx * 4 + c] = sum / 6;
        }
      }
    }

    const outImageData = new ImageData(result, W, H);
    const outCanvas = document.createElement("canvas");
    outCanvas.width = W;
    outCanvas.height = H;
    outCanvas.getContext("2d").putImageData(outImageData, 0, 0);

    const dataUrl = outCanvas.toDataURL("image/png");
    setResult(dataUrl);
    setMode("result");
    setStatus("✅ Done! Object removed successfully.");
    setProcessing(false);
  };

  const downloadResult = () => {
    if (!result) return;
    const a = document.createElement("a");
    a.href = result;
    a.download = "removed_object.png";
    a.click();
  };

  const resetAll = () => {
    setImage(null);
    setResult(null);
    setHasMask(false);
    setMode("idle");
    setStatus("");
    originalImageRef.current = null;
  };

  const cursorStyle = tool === "brush"
    ? `url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='${brushSize * 2}' height='${brushSize * 2}' viewBox='0 0 ${brushSize * 2} ${brushSize * 2}'%3E%3Ccircle cx='${brushSize}' cy='${brushSize}' r='${brushSize - 1}' fill='none' stroke='%2300ffc8' stroke-width='2'/%3E%3C/svg%3E") ${brushSize} ${brushSize}, crosshair`
    : `url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='${brushSize * 2}' height='${brushSize * 2}' viewBox='0 0 ${brushSize * 2} ${brushSize * 2}'%3E%3Ccircle cx='${brushSize}' cy='${brushSize}' r='${brushSize - 1}' fill='none' stroke='%23ff6060' stroke-width='2'/%3E%3C/svg%3E") ${brushSize} ${brushSize}, crosshair`;

  return (
    <div style={{
      minHeight: "100vh",
      background: "#0a0a0f",
      fontFamily: "'Syne', 'Space Grotesk', sans-serif",
      color: "#e8e8f0",
      display: "flex",
      flexDirection: "column",
    }}>
      <link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet" />

      {/* Header */}
      <div style={{
        padding: "18px 28px",
        borderBottom: "1px solid #1e1e2e",
        display: "flex",
        alignItems: "center",
        justifyContent: "space-between",
        background: "#0d0d18",
      }}>
        <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
          <div style={{
            width: 36, height: 36, borderRadius: 10,
            background: "linear-gradient(135deg, #00ffc8, #0077ff)",
            display: "flex", alignItems: "center", justifyContent: "center",
            fontSize: 18,
          }}>✂️</div>
          <div>
            <div style={{ fontSize: 18, fontWeight: 800, letterSpacing: "-0.5px", color: "#fff" }}>
              EraseMagic
            </div>
            <div style={{ fontSize: 11, color: "#555", marginTop: -2 }}>
              AI Object · Watermark · Logo Remover
            </div>
          </div>
        </div>
        {image && (
          <button onClick={resetAll} style={{
            background: "transparent", border: "1px solid #2a2a3e",
            color: "#888", padding: "6px 14px", borderRadius: 8,
            cursor: "pointer", fontSize: 13,
          }}>
            ← New Image
          </button>
        )}
      </div>

      <div style={{ flex: 1, display: "flex", overflow: "hidden" }}>
        {/* Main Canvas Area */}
        <div style={{ flex: 1, display: "flex", flexDirection: "column", overflow: "hidden" }}>

          {/* Upload / Canvas */}
          {mode === "idle" ? (
            <div
              onDrop={onDrop}
              onDragOver={(e) => e.preventDefault()}
              onClick={() => fileRef.current.click()}
              style={{
                flex: 1,
                display: "flex",
                flexDirection: "column",
                alignItems: "center",
                justifyContent: "center",
                cursor: "pointer",
                border: "2px dashed #1e1e3a",
                margin: 24,
                borderRadius: 20,
                background: "#0d0d18",
                transition: "border-color 0.2s",
              }}
            >
              <div style={{ fontSize: 56, marginBottom: 16 }}>🖼️</div>
              <div style={{ fontSize: 22, fontWeight: 700, color: "#fff" }}>
                Drop your image here
              </div>
              <div style={{ fontSize: 14, color: "#555", marginTop: 8 }}>
                or click to browse — PNG, JPG, WEBP supported
              </div>
              <div style={{
                marginTop: 24, padding: "10px 24px",
                background: "linear-gradient(135deg, #00ffc8, #0077ff)",
                borderRadius: 10, fontSize: 14, fontWeight: 600, color: "#000",
              }}>
                Choose Image
              </div>
              <input
                ref={fileRef}
                type="file"
                accept="image/*"
                style={{ display: "none" }}
                onChange={(e) => loadImage(e.target.files[0])}
              />
            </div>
          ) : mode === "result" ? (
            <div style={{
              flex: 1, display: "flex", flexDirection: "column",
              alignItems: "center", justifyContent: "center", padding: 24,
            }}>
              <div style={{ fontSize: 13, color: "#00ffc8", marginBottom: 12, fontWeight: 600 }}>
                {status}
              </div>
              <img
                src={result}
                style={{
                  maxWidth: "100%", maxHeight: "calc(100vh - 280px)",
                  borderRadius: 16, boxShadow: "0 0 40px #00ffc820",
                  border: "1px solid #1e1e3a",
                }}
              />
              <div style={{ display: "flex", gap: 12, marginTop: 20 }}>
                <button
                  onClick={downloadResult}
                  style={{
                    padding: "12px 28px",
                    background: "linear-gradient(135deg, #00ffc8, #0077ff)",
                    border: "none", borderRadius: 12,
                    color: "#000", fontWeight: 700, cursor: "pointer", fontSize: 15,
                  }}
                >
                  ⬇ Download PNG
                </button>
                <button
                  onClick={() => { setMode("editor"); setResult(null); redrawDisplay(); setStatus(""); }}
                  style={{
                    padding: "12px 28px",
                    background: "#1a1a2e", border: "1px solid #2a2a4e",
                    borderRadius: 12, color: "#aaa", cursor: "pointer", fontSize: 15,
                  }}
                >
                  ✏️ Edit Again
                </button>
              </div>
            </div>
          ) : (
            /* EDITOR MODE */
            <div style={{ flex: 1, position: "relative", overflow: "auto", padding: 12 }}>
              {status && (
                <div style={{
                  position: "absolute", top: 20, left: "50%",
                  transform: "translateX(-50%)", zIndex: 10,
                  background: "#0d1a2a", border: "1px solid #0077ff44",
                  borderRadius: 10, padding: "8px 18px",
                  fontSize: 13, color: "#00ffc8", whiteSpace: "nowrap",
                }}>
                  {status}
                </div>
              )}
              <canvas
                ref={displayCanvasRef}
                style={{
                  maxWidth: "100%",
                  maxHeight: "calc(100vh - 200px)",
                  borderRadius: 12,
                  cursor: cursorStyle,
                  display: "block",
                  margin: "0 auto",
                  boxShadow: "0 4px 40px #00000060",
                  border: "1px solid #1e1e3a",
                  touchAction: "none",
                }}
                onMouseDown={onPointerDown}
                onMouseMove={onPointerMove}
                onMouseUp={onPointerUp}
                onMouseLeave={onPointerUp}
                onTouchStart={onPointerDown}
                onTouchMove={onPointerMove}
                onTouchEnd={onPointerUp}
              />
            </div>
          )}
        </div>

        {/* Right Panel */}
        {mode === "editor" && (
          <div style={{
            width: 220,
            background: "#0d0d18",
            borderLeft: "1px solid #1e1e2e",
            padding: 16,
            display: "flex",
            flexDirection: "column",
            gap: 20,
            overflowY: "auto",
          }}>
            {/* Tool */}
            <div>
              <div style={{ fontSize: 11, color: "#555", fontWeight: 600, marginBottom: 10, letterSpacing: 1, textTransform: "uppercase" }}>
                Tool
              </div>
              <div style={{ display: "flex", gap: 8 }}>
                {[
                  { id: "brush", label: "🖌 Brush", color: "#00ffc8" },
                  { id: "eraser", label: "⬜ Eraser", color: "#ff6060" },
                ].map((t) => (
                  <button
                    key={t.id}
                    onClick={() => setTool(t.id)}
                    style={{
                      flex: 1, padding: "10px 0",
                      background: tool === t.id ? `${t.color}22` : "#16162a",
                      border: `1.5px solid ${tool === t.id ? t.color : "#1e1e3a"}`,
                      borderRadius: 10, color: tool === t.id ? t.color : "#555",
                      cursor: "pointer", fontSize: 12, fontWeight: 600,
                    }}
                  >
                    {t.label}
                  </button>
                ))}
              </div>
            </div>

            {/* Brush Size */}
            <div>
              <div style={{ fontSize: 11, color: "#555", fontWeight: 600, marginBottom: 10, letterSpacing: 1, textTransform: "uppercase" }}>
                Brush Size: {brushSize}px
              </div>
              <input
                type="range" min={4} max={80} value={brushSize}
                onChange={(e) => setBrushSize(Number(e.target.value))}
                style={{ width: "100%", accentColor: "#00ffc8" }}
              />
              <div style={{ display: "flex", justifyContent: "space-between", marginTop: 8, gap: 4 }}>
                {[8, 16, 28, 44, 64].map((s) => (
                  <button
                    key={s}
                    onClick={() => setBrushSize(s)}
                    style={{
                      width: 36, height: 36,
                      background: brushSize === s ? "#00ffc822" : "#16162a",
                      border: `1px solid ${brushSize === s ? "#00ffc8" : "#1e1e3a"}`,
                      borderRadius: 8, cursor: "pointer",
                      display: "flex", alignItems: "center", justifyContent: "center",
                    }}
                  >
                    <div style={{
                      width: Math.max(4, s / 5), height: Math.max(4, s / 5),
                      borderRadius: "50%",
                      background: brushSize === s ? "#00ffc8" : "#555",
                    }} />
                  </button>
                ))}
              </div>
            </div>

            {/* Auto Detect */}
            <div>
              <div style={{ fontSize: 11, color: "#555", fontWeight: 600, marginBottom: 10, letterSpacing: 1, textTransform: "uppercase" }}>
                AI Auto-Select
              </div>
              {[
                { type: "watermark", label: "💧 Watermark", color: "#6644ff" },
                { type: "logo", label: "🏷 Logo / Brand", color: "#ff8800" },
                { type: "object", label: "👤 Main Object", color: "#ff4488" },
              ].map(({ type, label, color }) => (
                <button
                  key={type}
                  onClick={() => autoDetect(type)}
                  disabled={autoDetecting || processing}
                  style={{
                    width: "100%", padding: "10px 0", marginBottom: 8,
                    background: `${color}18`,
                    border: `1px solid ${color}44`,
                    borderRadius: 10, color: color,
                    cursor: "pointer", fontSize: 13, fontWeight: 600,
                    opacity: autoDetecting ? 0.6 : 1,
                  }}
                >
                  {autoDetecting ? "⏳ Detecting..." : label}
                </button>
              ))}
              <div style={{ fontSize: 11, color: "#333", marginTop: 4, lineHeight: 1.5 }}>
                Claude AI automatically selects the object area for you.
              </div>
            </div>

            {/* Actions */}
            <div style={{ marginTop: "auto" }}>
              <button
                onClick={clearMask}
                disabled={processing || !hasMask}
                style={{
                  width: "100%", padding: "10px 0", marginBottom: 10,
                  background: "#16162a",
                  border: "1px solid #2a2a3e",
                  borderRadius: 10, color: "#666",
                  cursor: "pointer", fontSize: 13, fontWeight: 600,
                }}
              >
                🧹 Clear Mask
              </button>
              <button
                onClick={removeObject}
                disabled={processing || !hasMask}
                style={{
                  width: "100%", padding: "14px 0",
                  background: hasMask
                    ? "linear-gradient(135deg, #00ffc8, #0077ff)"
                    : "#1a1a2e",
                  border: "none",
                  borderRadius: 12,
                  color: hasMask ? "#000" : "#333",
                  cursor: hasMask ? "pointer" : "not-allowed",
                  fontSize: 15, fontWeight: 800,
                  transition: "all 0.2s",
                }}
              >
                {processing ? "⚙️ Processing..." : "✂️ Remove Object"}
              </button>
              {!hasMask && (
                <div style={{ fontSize: 11, color: "#333", marginTop: 8, textAlign: "center" }}>
                  Paint over object or use AI Auto-Select
                </div>
              )}
            </div>
          </div>
        )}
      </div>

      {/* Offscreen mask canvas */}
      <canvas ref={maskCanvasRef} style={{ display: "none" }} />

      {/* Bottom hint */}
      {mode === "editor" && (
        <div style={{
          padding: "10px 24px",
          borderTop: "1px solid #1a1a2a",
          fontSize: 12,
          color: "#333",
          display: "flex",
          gap: 24,
        }}>
          <span>🖌 <span style={{ color: "#555" }}>Paint red mask</span></span>
          <span>🤖 <span style={{ color: "#555" }}>Or use AI Auto-Select</span></span>
          <span>✂️ <span style={{ color: "#555" }}>Click Remove Object</span></span>
        </div>
      )}
    </div>
  );
}
