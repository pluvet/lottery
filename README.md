#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <ctype.h>
#include <windows.h>

#define NUM 9

void cargar_tickets(int ticket[][15], int N);
void contador_aciertos(int ticket[][15], int sorteo[], int aciertos[], int N);
void metodo_burbuja(int vector[], int n);
void mostrar_ganadores(int aciertos[], int N);
void mostrar_sorteo(int sorteo[]);
void mostrar_tickets(int ticket[][15], int N);
void ordenar_sorteo(int sorteo[]);
void ordenar_tickets(int ticket[][15], int N);
void simular_sorteo(int sorteo[]);
int  validar_cartones();

// Funciones nuevas
void setWindowSize(int width, int height);
void textColor(int numero);

int main()
{
	int  ticket[NUM][15], sorteo[15], aciertos[NUM], N;
	char resp;
	
	srand(time(NULL));
	
	setWindowSize(80, 51); // Ajustar tamaño de la ventana
	
	system("COLOR 1F");    // 1 = Fondo azul / F = Texto blanco brillante
	
	do
	{
		system("cls");
		
		printf("|=============================================================================|\n");
		printf("|                                   KINO                                      |\n");
		printf("|=============================================================================|\n");
		
		// 1. El usuario indicará cuantos cartones se deben generar	
		N = validar_cartones();
		
		// 2. Cargar cada ticket con números aleatorios entre 1 y 25, sin que se repitan	
		cargar_tickets(ticket, N);
		
		// Ordenar números de los tickets de menor a mayor	
		ordenar_tickets(ticket, N);
		
		// 3. Mostrar los tickets por pantalla, uno por uno, identificados por un código	
		mostrar_tickets(ticket, N);
			
		// 4. Simular un sorteo, deben generarse 15 números aleatorios entre 1 y 25, sin repetirse	
		simular_sorteo(sorteo);
		
		// Ordenar números del sorteo de menor a mayor	
		ordenar_sorteo(sorteo);
		
		// Mostrar números del sorteo
		mostrar_sorteo(sorteo);
		
		// Contador de aciertos por ticket		
		contador_aciertos(ticket, sorteo, aciertos, N);
		
		// Mostrar Ganador(es) con 12, 13, 14 y 15 aciertos
		mostrar_ganadores(aciertos, N);
		
		// 5. Permitir al usuario simular varios sorteos		
		printf("\n\n\n %cDesea realizar otro sorteo?: ", 168);
		scanf(" %c", &resp);
		
		resp = toupper(resp);
				
	}while(resp=='S');
	
	//getch();
	
	return 0;
}

void cargar_tickets(int ticket[][15], int N)
{
	int i, j, jj, band;
	
	for(i=0; i<N; i++)
	{
		for(j=0; j<15; j++)
		{
			do{
				ticket[i][j] = rand() % 25 + 1;
				
				band = 0; // No repetido
				
				for(jj=0; jj<j; jj++) //for(jj=j-1; jj>=0; jj--)
				{
					if(ticket[i][jj] == ticket[i][j])
					{
						band = 1; // Repetido
						break;
					}
				}				
			}while(band == 1);			
		}
	}
}

void contador_aciertos(int ticket[][15], int sorteo[], int aciertos[], int N)
{
	int i, j, jj;
	
	for(i=0; i<N; i++)
	{
		aciertos[i] = 0;
		
		for(j=0; j<15; j++)
		{
			for(jj=0; jj<15; jj++)
			{
				if(sorteo[jj] == ticket[i][j])
				{
					aciertos[i]++;
					break;
				}
			}
		}
		// printf("\n\n Aciertos Ticket %04d: %d aciertos", i+1, aciertos[i]);			
	}
}

void metodo_burbuja(int vector[], int n)
{
	int i, j, aux;
	
	for(i=0; i<n-1; i++)
	{
		for(j=i+1; j<n; j++)
		{
			if(vector[j] < vector[i])
			{
				aux = vector[j];
				vector[j] = vector[i];
				vector[i] = aux;
			}
		}
	}
}

void mostrar_ganadores(int aciertos[], int N)
{
	int i, j, band, cont;
	
	band = 0; // No existe ganador
	
	for(j=12; j<=15; j++) // Número de aciertos
	{
		cont = 0;
		
		for(i=0; i<N; i++)
		{
			if(aciertos[i] == j)
			{
				if(cont == 0)
				{
					textColor(31);
					printf("\n\n\n Ganadores con %d aciertos: ", j);
				}
				
				textColor(30);				
				printf("\n Ticket %04d", i+1);
				
				cont++;  // Contador de ganadores					
			}
		}
		
		if(cont > 0) band = 1; // Si hay ganadores
	}
		
	if(band == 0)
	{
		textColor(30);
		printf("\n\n\n No existen ganadores en este sorteo");
	}
	
	textColor(31);
}

void mostrar_sorteo(int sorteo[])
{
	int j;
	
	printf("\n\n\n ");
	textColor(241);			
	printf(" Sorteo (Carton Ganador) ");
	textColor(26);
	printf("\n");
	
	for(j=0; j<15; j++)
	{
		if(j % 3 == 0) printf("\n");
		
		printf("%6d ", sorteo[j]);
	}
	
	textColor(31);
}

void mostrar_tickets(int ticket[][15], int N)
{
	int i, j;
	
	for(i=0; i<N; i++)
	{
		printf("\n\n ");
		textColor(241);
		printf(" Ticket %04d ", i+1);
		textColor(30);
		printf("\n");
		
		for(j=0; j<15; j++)
		{
			if(j % 3 == 0) printf("\n");
			
			printf("%4d", ticket[i][j]);			
		}
	}	
}

void ordenar_sorteo(int sorteo[])
{
	metodo_burbuja(sorteo, 15);
}

void ordenar_tickets(int ticket[][15], int N)
{
	int i;
		
	for(i=0; i<N; i++)
	{
		metodo_burbuja(&ticket[i][0], 15);
	}
}

void simular_sorteo(int sorteo[])
{
	int j, jj, band;
	
	for(j=0; j<15; j++)
	{
		do
		{
			sorteo[j] = rand() % 25 + 1;
			
			band = 0; // No repetido	
					
			for(jj=0; jj<j; jj++) //for(jj=j-1; jj>=0; jj--)
			{
				if(sorteo[jj] == sorteo[j])
				{
					band = 1; // Repetido
					break;
				}
			}				
		}while(band == 1);	
	}	
}

int validar_cartones()
{
	int N;
	
	do
	{
		printf("\n\n Cuantos cartones desea generar: ");
		scanf("%d", &N);	
		
		if(N <= 2  ||  N >= NUM + 1)
		{
			textColor(28);
			printf("\n %cError! el numero de cartones debe ser minimo 3 - maximo %d \n", 173, NUM);
			textColor(31);
		}
			
	}while( N <= 2  ||  N >= NUM + 1);
	
	return N;	
}

void setWindowSize(int width, int height)
{
	COORD bufferSize;
	bufferSize.X = width;
	bufferSize.Y = 5000;

	SMALL_RECT rect;
	rect.Top    = 0;
	rect.Left   = 0;
	rect.Bottom = height;    // alto de la ventana de la consola
	rect.Right  = width - 1; // ancho de la ventana de la consola

	HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE); // get handle
	SetConsoleScreenBufferSize(handle, bufferSize);  // set buffer size		
	SetConsoleWindowInfo(handle, TRUE, &rect);       // set window size	
}

void textColor(int numero)
{
	SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), numero);
}
