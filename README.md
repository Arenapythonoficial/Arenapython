import datetime
import json
import os

# Lista ordenada de desafios (do mais fácil ao mais difícil)
daily_challenges = [
    {"id": 1, "description": "Escreva uma função que retorne o quadrado de um número.", "solution": "def square(x): return x * x"},
    {"id": 2, "description": "Escreva uma função que verifica se uma palavra é um palíndromo.", "solution": "def is_palindrome(s): return s == s[::-1]"},
    {"id": 3, "description": "Escreva uma função que soma todos os números em uma lista.", "solution": "def sum_list(lst): return sum(lst)"},
]

# Missão especial de quarta-feira
sunday_mission = {"id": 99, "description": "Crie um bot simples que responda 'Olá!' quando chamado.", "solution": "def bot(): return 'Olá!'"}

DATA_FILE = "users_data.json"

# Usuários pré-cadastrados
initial_users = {
    "Guilherme Ferreira": {"streak": 0, "score": 0, "ra": ""},
    "Gustavo Alves": {"streak": 0, "score": 0, "ra": ""},
    "João Pedro Ramos": {"streak": 0, "score": 0, "ra": ""},
    "Carlos Eduardo": {"streak": 0, "score": 0, "ra": ""},
    "Kaue Brendo Martins": {"streak": 0, "score": 0, "ra": ""},
    "Matheus Severiano": {"streak": 0, "score": 0, "ra": ""},
}

def load_users():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    else:
        save_users(initial_users)
        return initial_users

def save_users(users):
    with open(DATA_FILE, "w") as f:
        json.dump(users, f)

def show_ranking(users):
    print("\n=== Ranking ===")
    ranking = sorted(users.items(), key=lambda x: x[1]['score'], reverse=True)
    for i, (user, data) in enumerate(ranking, 1):
        print(f"{i}. {user} - Pontos: {data['score']} | Streak: {data['streak']} | RA: {data['ra']}")

def get_daily_challenge():
    today = datetime.datetime.now()
    if today.weekday() == 2:  # Quarta-feira (0=segunda, 1=terça, 2=quarta)
        return sunday_mission
    else:
        # Pega o desafio correspondente ao dia da semana (0 a 5 -> desafios 1 a 3, looping se precisar)
        index = today.weekday() % len(daily_challenges)
        return daily_challenges[index]

def main():
    users = load_users()
    print("=== Sistema de Desafios Diários em Python ===")
    username = input("Digite seu nome de usuário: ").strip()
    ra = input("Digite seu R.A.: ").strip()

    if username not in users:
        users[username] = {"streak": 0, "score": 0, "ra": ra}
    else:
        users[username]["ra"] = ra  # Atualiza RA caso o usuário já exista

    today = datetime.date.today()
    challenge = get_daily_challenge()
    print(f"\nDesafio de hoje ({today}): {challenge['description']}")
    user_input = input("Cole aqui sua solução (em uma linha): ").strip().replace(" ", "")

    expected_solution = challenge['solution'].replace(" ", "")
    if user_input == expected_solution:
        print("Correto! +5 pontos.")
        users[username]['score'] += 5
        users[username]['streak'] += 1
    else:
        print("Resposta errada. +0 pontos. Streak reiniciado.")
        users[username]['streak'] = 0

    save_users(users)
    show_ranking(users)

if __name__ == "__main__":
    main()
