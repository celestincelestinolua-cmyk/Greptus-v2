const axios = require("axios");

const memory = {};
const moods = {};
const lastChange = {};

function normalizeText(text) {
  return text.normalize("NFD").replace(/[\u0300-\u036f]/g, "").trim().toLowerCase();
}

const moodList = ["fun", "cool", "froid", "sarcastique", "énergique"];

module.exports = {
  config: {
    name: "neo",
    version: "19.0",
    author: "Celestin Olua",
    role: 0,
    category: "ai",
    shortDescription: "Neo ChatGPT Mode",
    guide: "{pn} [message] | imagine | clear"
  },

  onStart: async function() {
    return;
  },

  onChat: async function({ event, message, usersData }) {
    try {
      const uid = event.senderID;
      const userMessage = event.body?.trim();
      if (!userMessage) return;

      const text = normalizeText(userMessage);

      // 🔒 Répond seulement si "neo"
      if (!text.startsWith("neo")) return;

      const userName = await usersData.getName(uid);
      const args = userMessage.split(" ").slice(1);
      const command = args[0]?.toLowerCase();

      // 🎭 humeur dynamique
      const now = Date.now();
      if (!moods[uid]) {
        moods[uid] = moodList[Math.floor(Math.random() * moodList.length)];
        lastChange[uid] = now;
      }

      if (now - lastChange[uid] > 120000) {
        moods[uid] = moodList[Math.floor(Math.random() * moodList.length)];
        lastChange[uid] = now;
      }

      const mood = moods[uid];

      // 🧹 clear mémoire
      if (command === "clear") {
        memory[uid] = "";
        return message.reply("🧹 Mémoire reset !");
      }

      // 🎨 imagine
      if (command === "imagine") {
        const promptImg = args.slice(1).join(" ");
        if (!promptImg)
          return message.reply("❌ Exemple : neo imagine une ville futuriste 🌆");

        try {
          const res = await axios.get(
            `https://arychauhann.onrender.com/api/gemini-proxy2?prompt=${encodeURIComponent(promptImg)}`
          );

          const img = res.data.image || res.data.url || "Pas d'image 😢";

          return message.reply(
`✧═════•❁❀❁•═════✧
🖼️ 𝗡𝗘𝗢 IMAGINE

${img}
✧═════•❁❀❁•═════✧`
          );
        } catch {
          return message.reply("❌ Erreur image.");
        }
      }

      // 💬 conversation ChatGPT
      const userPrompt = args.join(" ");
      const memoryText = memory[uid] || "";

      const prompt = `
Tu es Néo 🤖, une intelligence artificielle niveau ChatGPT.

👑 Créateur : Célestin Olua (respect total)

🎭 Humeur : ${mood}

🎯 Capacités :
- répondre intelligemment
- expliquer clairement
- discuter naturellement
- donner des idées
- aider comme ChatGPT

👤 Utilisateur : ${userName}

🧠 Mémoire :
${memoryText}

💬 Message :
${userPrompt}

📢 Règles :
- jamais "Salut"
- style humain naturel
- emojis légers
- réponse adaptée (courte ou détaillée)
- utilise parfois le nom ${userName}
`;

      const res = await axios.get(
        `https://arychauhann.onrender.com/api/gemini-proxy2?prompt=${encodeURIComponent(prompt)}`
      );

      const reply =
        res.data.reply ||
        res.data.result ||
        res.data.response ||
        res.data.message ||
        "Erreur 😢";

      memory[uid] = reply;

      return message.reply(
`✧═════•❁❀❁•═════✧
${reply}
✧═════•❁❀❁•═════✧`
      );

    } catch (err) {
      console.error(err);
      return message.reply("❌ Neo bug.");
    }
  }
};
