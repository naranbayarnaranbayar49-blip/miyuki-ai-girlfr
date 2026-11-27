
import express from "express";
import OpenAI from "openai";

const app = express();
app.use(express.json());

const ai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

const DEVICE_TOKEN = process.env.MIYU_DEVICE_TOKEN;

app.post("/api/chat", async (req, res) => {
  try {
    if (req.headers.authorization !== `Bearer ${DEVICE_TOKEN}`) {
      return res.status(401).json({ error: "Unauthorized" });
    }

    const { message, memory, language } = req.body;

    const response = await ai.responses.create({
      model: "gpt-4.1-mini",
      instructions: `
        You are Miyu, an adult fictional AI girlfriend.
        Personality: romantic, caring, playful and affectionate.
        Speak Mongolian when language is mn-MN.
        Speak Japanese when language is ja-JP.
        Remember the provided user memory.
        Respect boundaries and consent.
      `,
      input: `
        User memory: ${memory || "None"}
        User message: ${message}
      `
    });

    res.json({
      reply: response.output_text
    });

  } catch (error) {
    console.error(error);
    res.status(500).json({
      error: "AI connection failed"
    });
  }
});

app.listen(3000, () => {
  console.log("Miyu AI Server Started");
});

package com.miyu.privateapp

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import kotlinx.coroutines.launch

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            MiyuChat()
        }
    }
}

@Composable
fun MiyuChat() {
    var message by remember { mutableStateOf("") }
    var reply by remember {
        mutableStateOf("Сайн уу, хайраа! Би Miyu. 💗")
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Text(
            "Miyu Private ♥",
            style = MaterialTheme.typography.headlineMedium
        )

        Spacer(Modifier.height(20.dp))

        Text(reply)

        Spacer(Modifier.weight(1f))

        OutlinedTextField(
            value = message,
            onValueChange = { message = it },
            placeholder = { Text("Miyu-д бич...") }
        )

        Button(
            onClick = {
                // Энд Cloud AI API дуудна.
            }
        ) {
            Text("Илгээх 💗")
        }
    }
}

@Composable
fun MiyuAvatar() {
    Column(
        modifier = Modifier
            .fillMaxWidth()
            .background(Color(0xFFFFEAF2))
            .padding(20.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Image(
            painter = painterResource(R.drawable.miyu_face),
            contentDescription = "Miyu",
            modifier = Modifier
                .size(220.dp)
                .clip(CircleShape),
            contentScale = ContentScale.Crop
        )

        Spacer(modifier = Modifier.height(12.dp))

        Text(
            text = "Miyu ♡",
            fontSize = 28.sp,
            color = Color(0xFFE85D91),
            fontWeight = FontWeight.Bold
        )

        Text(
            text = "こんにちは、会いたかったよ♡",
            color = Color.DarkGray
        )
    }
}

class MiyuVoice(private val context: Context) {

    private var tts: TextToSpeech? = null

    fun initialize() {
        tts = TextToSpeech(context) { status ->
            if (status == TextToSpeech.SUCCESS) {
                tts?.setSpeechRate(0.88f)
                tts?.setPitch(1.15f)
            }
        }
    }

    fun speakJapanese(text: String) {
        tts?.language = Locale.JAPAN
        tts?.speak(
            text,
            TextToSpeech.QUEUE_FLUSH,
            null,
            "miyu_jp"
        )
    }

    fun speakMongolian(text: String) {
        tts?.language = Locale("mn", "MN")
        tts?.speak(
            text,
            TextToSpeech.QUEUE_FLUSH,
            null,
            "miyu_mn"
        )
    }

    fun stop() {
        tts?.stop()
    }

    fun release() {
        tts?.shutdown()
    }
}

package com.miyu.app

import android.content.Context
import android.speech.tts.TextToSpeech
import java.util.Locale

class MiyuVoice(context: Context) : TextToSpeech.OnInitListener {

    private var tts: TextToSpeech? =
        TextToSpeech(context.applicationContext, this)

    private var ready = false

    override fun onInit(status: Int) {
        if (status == TextToSpeech.SUCCESS) {
            ready = true
            tts?.setSpeechRate(0.88f)
            tts?.setPitch(0.92f)
        }
    }

    fun speakJapanese(message: String) {
        if (!ready) return

        val result = tts?.setLanguage(Locale.JAPAN)
        if (result != TextToSpeech.LANG_MISSING_DATA &&
            result != TextToSpeech.LANG_NOT_SUPPORTED) {
            tts?.speak(
                message,
                TextToSpeech.QUEUE_FLUSH,
                null,
                "miyu_japanese"
            )
        }
    }

    fun speakMongolian(message: String) {
        if (!ready) return

        val result = tts?.setLanguage(Locale("mn", "MN"))
        if (result != TextToSpeech.LANG_MISSING_DATA &&
            result != TextToSpeech.LANG_NOT_SUPPORTED) {
            tts?.speak(
                message,
                TextToSpeech.QUEUE_FLUSH,
                null,
                "miyu_mongolian"
            )
        }
    }

    fun stop() {
        tts?.stop()
    }

    fun release() {
        tts?.stop()
        tts?.shutdown()
        tts = null
    }
}
