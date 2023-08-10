# bat_final

import numpy as np
import time
import matplotlib.pyplot as plt

def distance(city1, city2):
    return np.sqrt((city1[0] - city2[0]) ** 2 + (city1[1] - city2[1]) ** 2)

def fitness(solution, cities):
    return sum(distance(cities[solution[i]], cities[solution[i+1]]) for i in range(len(solution)-1)) + distance(cities[solution[-1]], cities[solution[0]])

def initialize_bats(cities, num_bats=20):
    return [np.random.permutation(len(cities)) for _ in range(num_bats)]

def update_bat(bat, best_bat, alpha, gamma):
    new_bat = np.copy(bat)
    
    # Probabilidade baseada em gamma
    if np.random.rand() > gamma:
        i, j = np.random.choice(len(bat), 2, replace=False)
        new_bat[i], new_bat[j] = new_bat[j], new_bat[i]
    else:
        # Combinação das soluções bat e best_bat com um fator baseado em alpha
        indices = np.random.choice(len(bat), size=int(alpha*len(bat)), replace=False)
        for idx in indices:
            new_bat[idx] = best_bat[idx]
    return new_bat

def bat_algorithm(cities, num_bats=20, max_iter=1000, alpha=0.5, gamma=0.5):
    bats = initialize_bats(cities, num_bats)
    best_bat = min(bats, key=lambda x: fitness(x, cities))
    
    for iteration in range(max_iter):
        for i in range(num_bats):
            if np.random.rand() < (1 - fitness(bats[i], cities)/fitness(best_bat, cities)):
                bats[i] = update_bat(bats[i], best_bat, alpha, gamma)
            
            if fitness(bats[i], cities) < fitness(best_bat, cities):
                best_bat = np.copy(bats[i])
                
    return best_bat, fitness(best_bat, cities)

def plot_cities_and_path(cities, solution):
    plt.scatter(cities[:, 0], cities[:, 1], c='blue')
    for i in range(-1, len(solution) - 1):
        plt.plot([cities[solution[i]][0], cities[solution[i+1]][0]], 
                 [cities[solution[i]][1], cities[solution[i+1]][1]], c='red')
    plt.show()

# 29 cidades aleatórias
cities = np.random.rand(29, 2) * 100

# Inicia o cronômetro
start_time = time.time()

solution, distance_travelled = bat_algorithm(cities, alpha=0.5, gamma=0.5)

# Calcula o tempo gasto
elapsed_time = time.time() - start_time

print("Melhor solução:", solution)
print("Distância percorrida:", distance_travelled)
print(f"Tempo de execução: {elapsed_time:.4f} segundos")

plot_cities_and_path(cities, solution)
