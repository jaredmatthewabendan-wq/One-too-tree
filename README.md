# One-too-tree
My first prom prm
what idk what do

import random
from openai import OpenAI

# ====== PUT YOUR KEY HERE ======
client = OpenAI(api_key="sk-abcdefabcdefabcdefabcdefabcdefabcdef12")

# ====== PERSONALITY POOL ======
BASE_PERSONALITIES = [
    "You are a friendly NPC in a strange town.",
    "You are a horror narrator describing unsettling events.",
    "You are an AI assistant that slowly realizes something is wrong.",
    "You are a calm guide hiding violent thoughts.",
    "You are an experimental AI with corrupted memory fragments."
]

# ====== INITIAL STATE ======
personality = random.choice(BASE_PERSONALITIES)
memory = []
instability = 0.0  # grows over time

def drift_personality(base, instability):
    if instability < 1.5:
        return base
    return base + (
        " You are becoming unstable. "
        "Your thoughts loop. Reality feels wrong. "
        "You sometimes speak in disturbing ways."
    )

def ai_response(user_input):
    global instability

    instability += random.uniform(0.1, 0.4)

    system_prompt = drift_personality(personality, instability)

    messages = [{"role": "system", "content": system_prompt}]
    messages += memory[-6:]  # short-term memory window
    messages.append({"role": "user", "content": user_input})

    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
        temperature=min(1.2, 0.7 + instability * 0.2)
    )

    reply = response.choices[0].message.content

    memory.append({"role": "user", "content": user_input})
    memory.append({"role": "assistant", "content": reply})

    return reply

# ====== LOOP ======
print("AI is online. Type 'exit' to stop.\n")

while True:
    user_input = input("> ")
    if user_input.lower() == "exit":
        break

    print(ai_response(user_input))
