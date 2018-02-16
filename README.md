Kviz igra

/*Nemanja Kazic NRT-49/16*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define BROJ_PITANJA 9      //Kada se menja broj pitanja dovoljno je ovde uneti novi broj

struct pitanje{             // definisanje strukture koja cuva pitanja koja se uzimaju iz fajla
	char tekst_pitanja[120];    //Pitanja moraju biti u formatu: PITANJE;PRVI_ODGOVOR;DRUGI_ODGOVOR;TRECI_ODGOVOR;CETVRTI_ODGOVOR;BROJ_TACNOG_ODGOVORA
	char odgovor1[30];          ////Fajl iz koga se izvlace pitanja mora nositi naziv "pitanja.txt"
	char odgovor2[30];
	char odgovor3[30];
	char odgovor4[30];
	char tacan_odgovor[10];
};

void pocetni_meni();
void pomoc();
void igra();
void pomoc_prijatelja(char n);

int main()
{
    pocetni_meni();
}

void pocetni_meni(){
    char odabir;
    printf("\n\t\t\t----------DOBRODOSLI U KVIZ----------");
    printf("\n\t\t\tAko zelite da zapocnete igru pritisnite Z");
    printf("\n\t\t\tAko vam treba pomoc pritisnite P\n");
    odabir=toupper(getchar());
    fflush(stdin);
  if (odabir=='Z')
	{
        igra();
	}
  else if(odabir=='P'){
        pomoc();
	}
}

void pomoc(){
    char odabir1;
    printf("\nU ovoj igri cete dobijati pitanja na koja treba da odgovorite tacno.");
    printf("\nDa biste tacno odgovorili na pitanje potrebno je da pritisnete broj koji mislite da obelezava tacan odgovor.");
    printf("\nImate pravo 2 puta da pogresite,a kada pogresite gubite pola osvojene sume.Treci put kada pogresite ispadate iz igre. ");
    printf("\n1.Pocetni ekran");
    printf("\n2.Zapocni igru\n");
    odabir1=getchar();
	fflush(stdin);
  	if (odabir1=='1'){
     	pocetni_meni();
	}
	else if(odabir1=='2'){
        igra();
	}
}

void igra(){

    int pomoc_prijatelja1=0;
    int broj_zivota=3;                   //Ovde se inicijalizuje broj mogucih gresaka
    int runda=1;                         //Brojac rundi
    int skor=100;                        //inicijalizovanje minimalnog rezultata na 100
    char *token;                         //Token za parsiranje stringa
    char izbor,tacno;
    int i, broj;
    char bafer[201];                     //Cuva jedan red iz datoteke
    FILE *fptr;
    struct pitanje *m;                   //Deklarisanje pokazivaca na niz struktura

    m=malloc(BROJ_PITANJA*sizeof(struct pitanje));   //Dinamicka dodela memorije
    if(m==NULL){
        printf("Greska prilikom dodele memorije");
        exit(1);
    }

    printf("\nZapocinje igra!!!\n");
    fptr = fopen("pitanja.txt", "r") ;
    if(fptr == NULL){
        fprintf(stderr, "Greska otvaranja datoteke");        //Provera
        exit(1);
    }

    for(i=0;i<BROJ_PITANJA;i++){                    //For petlja koja svakom iteracijom prolazi jedno pitanje
        fgets(bafer, 201, fptr);

      token = strtok(bafer,";");                    //Parsiranje stringa
       strcpy((m+i)->tekst_pitanja,token);
      token = strtok(NULL, ";");
       strcpy((m+i) ->odgovor1,token);
      token = strtok(NULL, ";");
       strcpy((m+i) ->odgovor2,token);
      token = strtok(NULL, ";");
       strcpy((m+i) ->odgovor3,token);
      token = strtok(NULL, ";");
       strcpy((m+i) ->odgovor4,token);
      token = strtok(NULL, ";");
       strcpy((m+i) ->tacan_odgovor,token);

      printf("\n%d.%s",runda,(m+i)->tekst_pitanja);            //Stampanje pitanja i opcija
      printf("\n1.%s",(m+i) ->odgovor1);
      printf("\t\t2.%s",(m+i) ->odgovor2);
      if(pomoc_prijatelja1==0)                                 //Provera da li je vec iskoriscena pomoc prijatelja
      printf("\t\t\t\tZa pomoc prijatelja Pritisni P");
      printf("\n3.%s",(m+i) ->odgovor3);
      printf("\t\t4.%s",(m+i) ->odgovor4);


        tacno=(m+i) ->tacan_odgovor[0];         //pretvaranje stringa u karakter radi lakseg uporedjivanja
        izbor=getchar();                                        //Uzima input od korisnika
        fflush(stdin);
    		if(izbor=='p' || izbor=='P'){                    // proverava da li je odabrana pomoc prijatelja
         	if(pomoc_prijatelja1==0) {
        		pomoc_prijatelja(tacno);
        		pomoc_prijatelja1=1;
        		izbor=getchar();
            fflush(stdin);
       		 }
       		 else{
           		printf("\nVec ste iskoristili pomoc prijatelja.Odaberite odgovor:");
            		izbor=getchar();
            		fflush(stdin);
       		 }
    		}
      if( izbor==tacno) {                                               // Provera odgovora
         printf("\nOdgovor je tacan!!!");
         printf("\nOsvojili ste %d dinara!!", skor);
         skor=skor*2;                                                     //eksponencijalno povecavanje nagrade
         printf("\nPrelazimo na sledece pitanje.\n");
      }
      else{
        printf("\nNetacno.");
        printf("\nPrelazimo na sledece pitanje.");
        broj_zivota--;
        skor=skor/2;
        printf("\nVasa nagrada je sada:%d dinara.\n",skor);
      }
       runda++;                                                 //inkrement runde pitanja
       if(broj_zivota==0){                                      //Provera da li je zavrsena igra
       		fclose(fptr);
       		printf("\n\nZavrsili ste igru,niste uspeli da pobedite.");
       		printf("\n\nDa li zelite da igrate opet?(D/N)");
       		fflush(stdin);
            izbor=toupper(getchar());
            if(izbor=='D')
                pocetni_meni();
            else                                                 //Ako igrac ne pritisne d, onda vise nije bitno sta je pritisnuo,program ce se zavrsiti
            		exit(0);
        }

            if (runda==BROJ_PITANJA+1){                                            //Zavrsni ekran za popednika
                printf("\n\t\t________________________________________");
                printf("\n\t\t________________________________________");
                printf("\n\t\t     CESTITAMO POBEDILI STE!!!!!!!!!    ") ;
                printf("\n\t\t________________________________________");
                printf("\n\t\t________________________________________");
                printf("\n\t\t     Vasa nagrada je:%d dinara!!! ",skor);
                exit(0);
            }

        }
}
void pomoc_prijatelja(char n){                                 //Funkcija za pomoc prijatelja
    if(n=='1'){
        printf("\nNetacni odgovori su pod brojem 2 i 4.");
        printf("\nVas odgovor je:");
    }
    else if(n=='2'){
        printf("\nNetacni odgovori su pod brojem 3 i 4.");
        printf("\nVas odgovor je:");
    }
    else if(n=='3'){
        printf("\nNetacni odgovori su pod brojem 1 i 2.");
        printf("\nVas odgovor je:");
    }
    else if(n=='4'){
        printf("\nNetacni odgovori su pod brojem 1 i 3.");
        printf("\nVas odgovor je:");
    }
}