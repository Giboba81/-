import numpy as np

class HungarianAlgorithm:
    def __init__(self, cost_matrix):
        self.cost_matrix = cost_matrix.copy()
        self.n = len(cost_matrix)
        self.m = len(cost_matrix[0]) if self.n > 0 else 0
        self.row_covered = [False] * self.n
        self.col_covered = [False] * self.m
        self.Z0_r = 0
        self.Z0_c = 0
        self.marked = np.zeros((self.n, self.m), dtype=int)
        self.path = np.zeros((self.n + self.m, 2), dtype=int)
        
    def find_uncovered_zero(self):
        for i in range(self.n):
            for j in range(self.m):
                if self.cost_matrix[i][j] == 0 and not self.row_covered[i] and not self.col_covered[j]:
                    return (i, j)
        return (-1, -1)
    
    def star_in_row(self, row):
        for j in range(self.m):
            if self.marked[row][j] == 1:
                return j
        return -1
    
    def find_star_in_col(self, col):
        for i in range(self.n):
            if self.marked[i][col] == 1:
                return i
        return -1
    
    def find_prime_in_row(self, row):
        for j in range(self.m):
            if self.marked[row][j] == 2:
                return j
        return -1
    
    def augment_path(self, path_count):
        for p in range(path_count + 1):
            i, j = self.path[p][0], self.path[p][1]
            if self.marked[i][j] == 1:
                self.marked[i][j] = 0
            else:
                self.marked[i][j] = 1
    
    def clear_covers(self):
        self.row_covered = [False] * self.n
        self.col_covered = [False] * self.m
    
    def erase_primes(self):
        for i in range(self.n):
            for j in range(self.m):
                if self.marked[i][j] == 2:
                    self.marked[i][j] = 0
    
    def step1(self):
        """Редукция строк: вычитаем минимальный элемент из каждой строки"""
        for i in range(self.n):
            min_val = min(self.cost_matrix[i])
            for j in range(self.m):
                self.cost_matrix[i][j] -= min_val
        return 2
    
    def step2(self):
        """Находим нуль в матрице. Если в его строке и столбце нет других звёзд,
        помечаем его звёздой. Продолжаем, пока все нули не будут рассмотрены"""
        for i in range(self.n):
            for j in range(self.m):
                if self.cost_matrix[i][j] == 0 and not self.row_covered[i] and not self.col_covered[j]:
                    self.marked[i][j] = 1
                    self.row_covered[i] = True
                    self.col_covered[j] = True
        self.clear_covers()
        return 3
    
    def step3(self):
        """Покрываем столбцы, содержащие звёзды. Если все столбцы покрыты,
        назначение найдено. Иначе переходим к шагу 4"""
        count = 0
        for i in range(self.n):
            for j in range(self.m):
                if self.marked[i][j] == 1 and not self.col_covered[j]:
                    self.col_covered[j] = True
                    count += 1
        if count >= self.n:
            return 7  # решение найдено
        return 4
    
    def step4(self):
        """Находим непокрытый нуль и помечаем его простым. Если в его строке нет звезды,
        переходим к шагу 5. Иначе покрываем строку и открываем столбец со звездой.
        Продолжаем, пока не останется непокрытых нулей. Затем переходим к шагу 6"""
        while True:
            i, j = self.find_uncovered_zero()
            if i == -1:
                return 6
            self.marked[i][j] = 2
            star_col = self.star_in_row(i)
            if star_col == -1:
                self.Z0_r = i
                self.Z0_c = j
                return 5
            self.row_covered[i] = True
            self.col_covered[star_col] = False
    
    def step5(self):
        """Строим чередующуюся последовательность из звёзд и простых нулей.
        Увеличиваем последовательность, инвертируя звёзды и простые нули.
        Снимаем все покрытия и возвращаемся к шагу 3"""
        path_count = 0
        self.path[path_count][0] = self.Z0_r
        self.path[path_count][1] = self.Z0_c
        while True:
            row = self.find_star_in_col(self.path[path_count][1])
            if row == -1:
                break
            path_count += 1
            self.path[path_count][0] = row
            self.path[path_count][1] = self.path[path_count - 1][1]
            
            col = self.find_prime_in_row(self.path[path_count][0])
            path_count += 1
            self.path[path_count][0] = self.path[path_count - 1][0]
            self.path[path_count][1] = col
        
        self.augment_path(path_count)
        self.clear_covers()
        self.erase_primes()
        return 3
    
    def step6(self):
        """Находим минимальный непокрытый элемент, добавляем его к покрытым строкам
        и вычитаем из непокрытых столбцов. Возвращаемся к шагу 4"""
        min_val = float('inf')
        for i in range(self.n):
            if not self.row_covered[i]:
                for j in range(self.m):
                    if not self.col_covered[j] and self.cost_matrix[i][j] < min_val:
                        min_val = self.cost_matrix[i][j]
        
        for i in range(self.n):
            for j in range(self.m):
                if self.row_covered[i]:
                    self.cost_matrix[i][j] += min_val
                if not self.col_covered[j]:
                    self.cost_matrix[i][j] -= min_val
        return 4
    
    def compute(self):
        """Основная функция, выполняющая шаги алгоритма до нахождения решения"""
        step = 1
        while step < 7:
            if step == 1:
                step = self.step1()
            elif step == 2:
                step = self.step2()
            elif step == 3:
                step = self.step3()
            elif step == 4:
                step = self.step4()
            elif step == 5:
                step = self.step5()
            elif step == 6:
                step = self.step6()
        
        # Собираем результаты
        results = []
        for i in range(self.n):
            for j in range(self.m):
                if self.marked[i][j] == 1:
                    results.append((i, j))
        return results
    
    def get_total_cost(self, assignments):
        """Вычисляет общую стоимость назначений"""
        total = 0
        for i, j in assignments:
            total += self.cost_matrix[i][j]
        return total
