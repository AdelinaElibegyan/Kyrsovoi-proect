/*

* Файл: main.c 

* Автор: А. В. Элибегян 

* Программа для анализа кусочно-заданной функции f(x). 

* Назначение: вычисление значения функции в точке, построение таблицы значений, 

* поиск экстремумов и приближенное решение уравнения f(x) = Y, а также 

* вычисление производной в точке. 

*/
#define _CRT_SECURE_NO_WARNINGS 
#include <stdio.h> 
#include <locale.h> 
#define _USE_MATH_DEFINES 
#include <math.h> 
#include <stdlib.h> 
#include <float.h> 

#define SCREENW 60 
#define SCREENH 40 

double F(double x) { 
 if (x < -1.0) return exp(sin(x)); 
 иначе, если (x>= -1.0 && x < 0.0) { 
 if (x == 0.0) return 0.0; 
 return log(x + 2.0) / x; 
    }
 else // x >= 0 
    {
 double sum = 0.0; 
 for (int n = 0; n <= 5; ++n) { 
 двойной числитель = pow(-1.0, n) * pow(x, n + 2); 
 двойной знаменатель = 1,0; 
 for (int i = 1; i <= n + 2; ++i) 
 знаменатель *= i; // Вычисление факториала (n+2)! 
 sum += числитель / знаменатель; 
        }
 возвращаемая сумма; 
    }
}

// Функция для вывода таблицы значений на интервале 
void pT(double minx, double maxx, double h, double (*function)(double), char skob1, char skob2) 
{
 printf("x\t|\tf(x)\n"); 
 printf("Начало\n"); 

 double start_x = minx; 
 double end_x = maxx; 

 if (skob1 == '(') start_x += h; 
 if (skob2 == ')') end_x -= h; 

 для (double x = start_x; x <= end_x; x += h) 
    {
 printf("%.2lf\t|\t%.6lf\n", x, function(x)); 
    }

 printf("Конец\n"); 
}

// Функция для построения графика 
typedef double (*TFunc)(double); 
void plot(double x0, double x1, TFunc f) 
{
 char screen[SCREENW][SCREENH]; 
 double x, y[SCREENW]; 
 double ymin = 0, ymax = 0; 
 двойной hx, hy;  
 int i, j; 
 int xz, yz; 

 hx = (x1 - x0) / (SCREENW - 1); 

 для (i = 0, x = x0; i < SCREENW; ++i, x += hx) 
    {
 y[i] = f(x); 
 if (y[i] < ymin) ymin = y[i]; 
 если (y[i] > ymax) ymax = y[i]; 
    }

 hy = (ymax - ymin) / (SCREENH - 1); 
 yz = (int)floor(ymax / hy + 0,5); 
 xz = (int)floor((0. - x0) / hx + 0.5); 

 for (j = 0; j < SCREENH; ++j) 
    {
 for (i = 0; i < SCREENW; ++i) 
        {
 if (j == yz && i == xz) 
 screen[i][j] = '+'; 
 else if (j == yz) 
 screen[i][j] = '-'; 
 else if (i == xz) 
 screen[i][j] = '|'; 
 ещё 
 screen[i][j] = ' '; 
        }
    }

 for (i = 0; i < SCREENW; ++i) 
    {
 j = (int)floor((ymax - y[i]) / hy + 0,5); 
 if (j >= 0 && j < SCREENH) // Защита от выхода за границы массива 
 screen[i][j] = '*'; 
    }

 for (j = 0; j < SCREENH; ++j) 
    {
 for (i = 0; i < SCREENW; ++i) putchar(screen[i][j]); 
 putchar('\n'); 
    }
}

// Поиск минимума и максимума на отрезке 
void findExtremes(double minx, double maxx, double h, double (*function)(double)) 
{
 double x_min = minx, x_max = minx; 
 double y_min = function(minx), y_max = function(minx); 

 для (double x = minx + h; x <= maxx; x += h) { 
 double y = function(x); 
 if (y < y_min) { 
 y_min = y; 
 x_min = x; 
        }
 if (y > y_max) { 
 y_max = y; 
 x_max = x; 
        }
    }

 printf("Минимум: f(%.4lf) = %.6lf\n", x_min, y_min); 
 printf("Максимум: f(%.4lf) = %.6lf\n", x_max, y_max); 
}

// Поиск x, при котором f(x) ? y (метод перебора с заданным шагом) 
void f_x(double minx, double maxx, double h, double Y, double (*function)(double)) 
{
 double best_x = minx; 
 double min_diff = fabs(function(minx) - Y); 

 для (double x = minx + h; x <= maxx; x += h) 
    {
 double diff = fabs(function(x) - Y); 
 if (разница < min_diff) 
        {
 min_diff = diff; 
 best_x = x; 
        }
    }

 printf("Ближайшее значение x, при котором f(x) ? %.6lf: x = %.4lf (f(x) = %.6lf)\n", 
 Y, best_x, function(best_x)); 
}

// Проверка монотонности на отрезке 
void checkMonotonicity(double minx, double maxx, double h, double (*function)(double)) { 
 double prev_y = function(minx); 
 int increasing = 1; // возрастание 
 int decreasing = 1; // убывание 

 для (double x = minx + h; x <= maxx; x += h) { 
 double y = function(x); 
 if (y > prev_y) decreasing = 0; // если есть рост, то не убывает 
 else if (y < prev_y) increasing = 0; // если есть уменьшенеи, то не возрастает 
 prev_y = y; 
    }
 если (увеличение && !уменьшение) 
 printf("Функция монотонно возрастает на отрезке [%.2lf, %.2lf]\n", minx, maxx); 
 else if (!увеличивается && уменьшается) 
 printf("Функция монотонно убывает на отрезке [%.2lf, %.2lf]\n", minx, maxx); 
 else if (увеличение и уменьшение) 
 printf("Функция постоянна на отрезке [%.2lf, %.2lf]\n", minx, maxx); 
 ещё 
 printf("Функция не является монотонной на отрезке [%.2lf, %.2lf]\n", minx, maxx); 
}

void clear_stdin() { 
 int c; 
 while ((c = getchar()) != '\n' && c != EOF); 
}

int main() { 
 setlocale(LC_ALL, "RUS"); 
 int n; 
 double x, minx, maxx, h, Y; 
 char skob1 = '.', skob2 = '.'; 
 char buffer[100]; 

 в то время как (1) { 
 printf("\n=== Меню ===\n"); 
 printf("1. Значение функции f(x) в точке.\n"); 
 printf("2. Таблица значений функции f(x) на интервале.\n"); 
 printf("3. Построить график функции f(x).\n"); 
 printf("4. Найти минимум и максимум на отрезке.\n"); 
 printf("5. Найти x, при котором f(x) ? Y.\n"); 
 printf("6. Проверить монотонность на отрезке.\n"); 
 printf("7. Завершить работу.\n"); 
 printf("> "); 

 if (scanf("%d", &n) != 1) { 
 clear_stdin(); 
 printf("Ошибка ввода.\n"); 
 продолжать; 
        }
 clear_stdin(); // Очистка после ввода числа 

 переключатель (n) { 
 случай 1: 
 printf("Введите значение x: "); 
 fgets(buffer, sizeof(buffer), stdin); 
 sscanf(buffer, "%lf", &x); 
 printf("f(%.2lf) = %.6lf\n", x, F(x)); 
 перерыв; 

 случай 2: 
 printf("Введите промежуток x, включая скобки (например, [-1,0 ; 1,0]): "); 
 fgets(buffer, sizeof(buffer), stdin); 
 sscanf(buffer, "%c%lf ; %lf%c", &skob1, &minx, &maxx, &skob2); 

 if ((skob1 == '[' || skob1 == '(') && (skob2 == ']' || skob2 == ')')) { 
 printf("Введите шаг h: "); 
 fgets(buffer, sizeof(buffer), stdin); 
 sscanf(buffer, "%lf", &h); 

 printf("Таблица значений функции f(x):\n"); 
 pT(minx, maxx, h, F, skob1, skob2); 
            }
 остальное { 
 printf("Ошибка: неправильный формат скобок!\n"); 
            }
 перерыв; 

 случай 3: 
 printf("Построение графика функции f(x):\n"); 
 printf("Введите промежуток x (минимальное и максимальное значения): "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf ; %lf", &minx, &maxx) != 2) { 
 printf("Ошибка ввода! Используйте формат: -1 ; 1\n"); 
 перерыв; 
            }
 plot(minx, maxx, F); 
 перерыв; 

 случай 4: 
 printf("Поиск экстремумов на отрезке:\n"); 
 printf("Введите промежуток x (мин ; макс): "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf ; %lf", &minx, &maxx) != 2) { 
 printf("Ошибка ввода! Используйте формат: -1 ; 1\n"); 
 перерыв; 
            }

 printf("Введите шаг h для поиска: "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf", &h) != 1) { 
 printf("Ошибка при вводе шага!\n"); 
 перерыв; 
            }

 findExtremes(minx, maxx, h, F); 
 перерыв; 

 случай 5: 
 printf("Поиск x, при котором f(x) ? Y:\n"); 
 printf("Введите промежуток x (мин ; макс): "); 
 fgets(buffer, sizeof(buffer), stdin); 
            if (sscanf(buffer, "%lf ; %lf", &minx, &maxx) != 2) {
                printf("Ошибка ввода!\n");
                break;
            }

            printf("Введите шаг h для поиска: ");
            fgets(buffer, sizeof(buffer), stdin);
 if (sscanf(buffer, "%lf", &h) != 1) { 
 printf("Ошибка ввода!\n"); 
 перерыв; 
            }

 printf("Введите значение Y: "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf", &Y) != 1) { 
 printf("Ошибка ввода!\n"); 
 перерыв; 
            }

 f_x(minx, maxx, h, Y, F); 
 перерыв; 

 случай 6: 
 printf("Проверка монотонности на отрезке:\n"); 
 printf("Введите промежуток x (мин ; макс): "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf ; %lf", &minx, &maxx) != 2) { 
 printf("Ошибка ввода!\n"); 
 перерыв; 
            }

 printf("Введите шаг h для проверки: "); 
 fgets(buffer, sizeof(buffer), stdin); 
 if (sscanf(buffer, "%lf", &h) != 1) { 
 printf("Ошибка ввода!\n"); 
 перерыв; 
            }

 checkMonotonicity(minx, maxx, h, F); 
 перерыв; 

 случай 7: 
 printf("Завершение работы.\n"); 
 возвращает 0; 

 По умолчанию: 
 printf("Неверный выбор. Попробуйте еще раз.\n"); 
 перерыв; 
        }
 printf("\n"); 
    }
}
