const { default: makeWASocket, useMultiFileAuthState, DisconnectReason } = require("@whiskeysockets/baileys")

async function startBot() {
    const { state, saveCreds } = await useMultiFileAuthState("auth_info")

    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true // Mostra QR no terminal
    })

    sock.ev.on("creds.update", saveCreds)

    sock.ev.on("messages.upsert", async ({ messages }) => {
        const msg = messages[0]

        if (!msg.message) return
        const text = msg.message.conversation || msg.message.extendedTextMessage?.text

        if (text) {
            console.log("Mensagem recebida:", text)

            if (text.toLowerCase() === "oi") {
                await sock.sendMessage(msg.key.remoteJid, { text: "👋 Olá, sou seu bot WhatsApp!" })
            }

            if (text.toLowerCase() === "menu") {
                await sock.sendMessage(msg.key.remoteJid, { text: "📌 Opções:\n1️⃣ Info\n2️⃣ Ajuda\n3️⃣ Contato" })
            }
        }
    })
}

startBot()
