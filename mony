import express from "express";
import fetch from "node-fetch";

const app = express();
app.use(express.json());

const SECRET = process.env.WORKER_SECRET;

app.post("/redeem", async (req, res) => {
  const auth = req.headers["x-auth-token"];
  if (auth !== SECRET) return res.status(401).json({ error: "Unauthorized" });

  const { voucher, phone } = req.body;
  if (!voucher || !phone) return res.status(400).json({ error: "Missing voucher or phone" });

  try {
    const tmResp = await fetch(`https://gift.truemoney.com/campaign/vouchers/${voucher}/redeem`, {
      method: "POST",
      headers: {
        "Accept": "application/json, text/javascript, */*; q=0.01",
        "Content-Type": "application/json",
        "Origin": "https://gift.truemoney.com",
        "Referer": "https://gift.truemoney.com/",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
      },
      body: JSON.stringify({ mobile: phone, voucher_hash: voucher })
    });

    const text = await tmResp.text();
    try {
      const data = JSON.parse(text);
      return res.json({ status: tmResp.status, data });
    } catch {
      return res.json({ status: tmResp.status, raw: text.slice(0, 500) });
    }
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(3000, () => console.log("✅ TrueMoney Proxy Ready on Port 3000"));
