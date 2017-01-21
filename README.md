# TypeRace1
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>
#include <ctype.h>
#include <windows.h>
#include <conio.h>

double word_score;
double level_score[3];
double total_score[3];
double total_time;
int size;
int level;
int file_number = -1;
char *name;
int counter = -1;
struct user player;
int line;
clock_t start2;
clock_t end2;
int words;
int chars;
int scoreset;

struct user{
    char user_name[20];
    double user_score[3];
    int user_level;
};


/*************************************************
                  Setcolor
*************************************************/

void SetColor(int ForgC)
{
    WORD wColor;
    //This handle is needed to get the current background attribute

    HANDLE hStdOut = GetStdHandle(STD_OUTPUT_HANDLE);
    CONSOLE_SCREEN_BUFFER_INFO csbi;
    //csbi is used for wAttributes word

    if(GetConsoleScreenBufferInfo(hStdOut, &csbi))
    {
        //To mask out all but the background attribute, and to add the color
        wColor = (csbi.wAttributes & 0xF0) + (ForgC & 0x0F);
        SetConsoleTextAttribute(hStdOut, wColor);
    }
    return;
}

/*************************************************
                   FILE
*************************************************/

int file_reader(FILE *f, char a[][20])
{
    rewind(f);
    int i=0;
    while (1){
        if(fscanf(f, "%s ", &a[i])==EOF)
        break;
        i++;
    }
    return i;
} //return the number of words, make an array of the words.


void FileNumber()
{
    int i;
    char file_level[100];
    FILE *fp;
    char file_name2[100] = ".txt";

    for(i = 1; fp != NULL; i++){
        char file_name1[100] = "level-";
        sprintf (file_level, "%d", i);
        strcat (file_name1,file_level);
        strcat (file_name1,file_name2);
        fp = fopen (file_name1 , "r");
        file_number++;
    }
}


int delete_element(char *fname, char *searchname)
{
    fname = "users";
	FILE *fp;
	FILE *fp_tmp;
	struct user myrecord;

	fp=fopen(fname, "rb");
	fp_tmp=fopen("tmp1", "wb");

	while (fread(&myrecord,sizeof(myrecord),1,fp) != NULL) {
		if (strcmp (searchname, myrecord.user_name) == 0);
		else {
			fwrite(&myrecord, sizeof(myrecord), 1, fp_tmp);
		}
	}
	fclose(fp);
	fclose(fp_tmp);

	remove(fname);
	rename("tmp1", fname);
}

/*************************************************
                  gotoxy
*************************************************/
COORD coord={0,0};

 void gotoxy(int x,int y)
 {
   coord.X=x;
 coord.Y=y;
 SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE),coord);
 }

 void diagram(void)
{

    SetColor(11);
    int i,j;
    gotoxy (10,3);
    for(i=0; i<50; i++){
        printf("%c", 220);
    }

    gotoxy (10,3);
    for(j=0; j<20; j++){
        gotoxy(10, (3+j));
        printf("%c", 220);

    }

    gotoxy (61,3);
    for(j=0; j<20; j++){
        gotoxy(60, (3+j));
        printf("%c", 220);
        }

    gotoxy (10,23);
    for(i=0; i<51; i++){
        printf("%c", 220);
    }


    SetColor(9);
    gotoxy (30,(5+line));
    //gotoxy(0, line );
    //printf("startario");

}

/*************************************************
                  User
*************************************************/


void save()
{
    FILE *fp;
    struct user save;
    int i;

    for (i=0; name[i]!= '\0'; i++){
        save.user_name[i] = name[i];
    }
    save.user_name[i] = '\0';
    save.user_score[0] = total_score[0];
    save.user_score[1] = total_score[1];
    save.user_score[2] = total_score[2];
    save.user_level = level;


    if (counter != -1){
        fp = fopen ("users", "rb+");
        rewind (fp);
        fseek (fp , counter * sizeof(save), SEEK_CUR);
        fwrite (&save, sizeof(save), 1, fp);
        fclose (fp);
    }
    else{
        fp = fopen ("users", "ab");
        fwrite (&save, sizeof(save), 1, fp);
        fclose (fp);
    }

    if (fp == NULL){
        printf ("sry we could't save Ur game :-(\n");
        exit (0);
    }

}

void find_user()
{

    FILE *fp;
    int check;
    fp = fopen ("users", "r+b");
    rewind (fp);
    while( strcmp(name, player.user_name)!=0){
        check = fread (&player, sizeof(player), 1, fp);
        counter++;
        if( check < 1 ){
            fclose (fp);
            counter = -1;
            return 0;
        }
    }

    //printf(" user_name = %s, user_level = %d, user_score = %f", player.user_name, player.user_level, player.user_score);
    //printf("\n\n name = %s, level = %d, score = %f, counter = %d\n\n", name, level, total_score, counter  );
    fclose (fp);
}

void TopUsers()
{


        FILE *fp, *f, *f_tmp;
        int i, j, k, check;
        fp = fopen("users", "rb");
        f = fopen("tmp", "wb");

        struct user top_users[10];
        struct user tmp;

        i = 0;
        int index = 0;
        int counter1 = 0;



        while(1){
                check = fread (&top_users[index], sizeof(tmp), 1, fp);
                if( check < 1 )
                    break;
                fwrite (&top_users[index], sizeof(tmp), 1, f);
                counter1++;
        }
        rewind (fp);

        while(index < 10){
            top_users[index].user_score[0] = 0;
            top_users[index].user_score[1] = 0;
            top_users[index].user_score[2] = 0;
            if( index >= counter1 )
                break;
            index++;
        }



        fclose (fp);
        fclose (f);

        if (scoreset == 1){
            for (i=0; i<10; i++){
                f = fopen("tmp", "rb+");
                f_tmp = fopen ("tmp2", "wb+");


                while (fread(&tmp, sizeof(tmp), 1, f) != NULL) {
                    if(tmp.user_score[0] >= top_users[i].user_score[0]){
                        top_users[i].user_score[0] = tmp.user_score[0];
                        top_users[i].user_score[1] = tmp.user_score[1];
                        top_users[i].user_score[2] = tmp.user_score[2];

                        for (k=0; tmp.user_name[k]!= '\0'; k++){
                            top_users[i].user_name[k] = tmp.user_name[k];
                        }
                        top_users[i].user_name[k] = '\0';

                    }

                }

                rewind (f);
                while (fread(&tmp, sizeof(tmp), 1, f) != NULL) {
                    if(strcmp (tmp.user_name, top_users[i].user_name) == 0);
                    else
                        fwrite(&tmp, sizeof(tmp), 1, f_tmp);
                }

                fclose (f);
                fclose (f_tmp);
                remove ("tmp");
                rename ("tmp2", "tmp");

            }
        }
            else{
                for (i=0; i<10; i++){
                f = fopen("tmp", "rb+");
                f_tmp = fopen ("tmp2", "wb+");


                while (fread(&tmp, sizeof(tmp), 1, f) != NULL) {
                    if(tmp.user_score[1] >= top_users[i].user_score[1]){
                        top_users[i].user_score[0] = tmp.user_score[0];
                        top_users[i].user_score[1] = tmp.user_score[1];
                        top_users[i].user_score[2] = tmp.user_score[2];

                        for (k=0; tmp.user_name[k]!= '\0'; k++){
                            top_users[i].user_name[k] = tmp.user_name[k];
                        }
                        top_users[i].user_name[k] = '\0';

                    }

                }

                rewind (f);
                while (fread(&tmp, sizeof(tmp), 1, f) != NULL) {
                    if(strcmp (tmp.user_name, top_users[i].user_name) == 0);
                    else
                        fwrite(&tmp, sizeof(tmp), 1, f_tmp);
                }

                fclose (f);
                fclose (f_tmp);
                remove ("tmp");
                rename ("tmp2", "tmp");

            }

        }

        fclose(fp);

        system ("cls");
        printf("Top 10 users:\n\n");
        printf("\t\t%s\t\t\t%s\n\n", "Name", "Score");

        for (i=0; i<10; i++){
            if ( i > (index -1) )
                break;


            printf ("\t\t%d.%s", (i+1), top_users[i].user_name);
            gotoxy (40,i+4);
            if (scoreset == 1){
                printf ("%f\n",top_users[i].user_score[0]);
            }
            else if (scoreset == 2){
                printf ("%f\n",top_users[i].user_score[1]);
            }
            else if (scoreset == 3){
                printf ("%f\n",top_users[i].user_score[2]);
            }
            else{
                printf ("Invalid input\n");
                exit (0);
            }

        }

        fclose (f);


}


/*************************************************
                   Make Link-list
*************************************************/

struct node
{
    char *word;
    struct node *next;
};


struct node* create_node(char *str)
{
    struct node *s;
    s = (struct node *)malloc(sizeof(struct node));
    if (s==NULL){
        printf("couldn't malloc :(\n");
        exit(-1);
    }
    s->word = str;
    s->next = NULL;
    return s;
}


void add_end(struct node *list, struct node *new_node)
{
    struct node * current;
    for (current = list; current->next != NULL; current = current->next);
    current->next = new_node;
    new_node->next = NULL;
}


struct node* create_set(FILE *f)
{
    int i;
    char a[1000][20];
    size = file_reader(f,a);

    struct node *list, *new_node;
    list = create_node(a[0]);
    for(i = 1; i < size; i++){
        new_node = create_node(a[i]);
        add_end(list,new_node);
    }

    return list;
} //make a list of the file's words .

void print_node(struct node *list)
{
    struct node *current;
    for (current = list; current != NULL; current = current->next)
        printf("%s ", current->word);
}

/*************************************************
                   display
*************************************************/
int random(struct node *list)
{
    struct node *current;
    int n=0;
    for (current = list; current != NULL; current = current->next)
         n++;

    time_t t = time(NULL);
    srand(t);
    int rand_node;
    rand_node = (rand()%n) + 1;
    return rand_node;
}//return a random link-list node.

struct node* reach_node(struct node *list, int n)
{
    struct node *current = list;
    int i;
    for (i = 0; i < n-1; i++){
        current = current -> next;
    }
    return current;
}

void delete_node(struct node **list, int n)
{
    if (n == 1){

        struct node *tmp, *current;
        current = *list;
        tmp = current;
        current = current->next;
        free(tmp);
        *list = current;
    }

    else{
        int i;
        struct node *tmp, *current;
        current = *list;

        for (i=0; i < (n - 2); i++){
            current = current->next;
        }

        tmp = current->next;
        current->next = current->next->next;
        free(tmp);
    }
}


int show (struct node **list)
{

    struct node *current = *list;
    int n,m;
    char *a;

    n = random (*list);
    current = reach_node (*list,n);
    a = current->word;
    delete_node (&*list, n);
    start2 = clock();
    m = play(a);
    if (m == 0){
        return 0;
    }
    words++;
    return 1;
}



/*************************************************
                   playing
*************************************************/

int play(char *a)
{

    char b[20];
    char key;
    int fault, length, i, counter, check_color;
    length = strlen(a);
    counter=0;
    fault=0;
    check_color = 0;

    for (i=0; i<length; i++){
        b[i]=a[i];
    }
    char c;
    double tmp_time=0;
    double time_spent;

    gotoxy (30,(5+line));

    for(i=0; i<length; i++){
        printf ("%c", b[i]);
    }

    clock_t start = clock();
    while (counter < length){
        if (line > 17){
            system("cls");
            total_score[0] += level_score[0];
            total_score[1] = level_score[1];
            total_score[2] = level_score[2];
            printf("You lose! :( \n\n");
            printf("This level Score :\n %f\n\nTotal Score:\n %f\n %f WPM\n %f CPM\n\n", level_score[0], total_score[0], total_score[1], total_score[2]);
            delete_element("fgd", name);
            exit (0);

        }
        key = kbhit();
        if (key){
            c = getch();

            if (c == 'Q'){
                clock_t end = clock ();
                time_spent = (double)(end - start) / CLOCKS_PER_SEC;
                total_time += (time_spent)/60;
                return 0;
            }
            if (c =='P'){
                clock_t start1 = clock();

                while (1){
                    c = getch ();
                    if (c == 'R'){
                        clock_t end1 = clock();
                        tmp_time += (double)(end1 - start1) / CLOCKS_PER_SEC;
                        break;
                    }
                }
            }

            if (c == a[counter]){
                chars++;
                check_color = 1;
                b[counter]= toupper (b[counter]);
                counter++;
                gotoxy (30, (4+line));
                for (i=0; i< 20; i++){
                    printf (" ");
                }
                gotoxy (30,(5+line));

                for (i = 0; i < counter; i++)
                    printf("%c", b[i]);


                SetColor(10);

                for (i = counter; i < (counter + 1); i++)
                    printf("%c", b[i]);

                SetColor(9);


                for (i = (counter + 1); i < length; i++)
                    printf("%c", b[i]);
            }
            else{
                check_color = -1;
                fault++;
                gotoxy (30, (4+line));
                for (i=0; i< 20; i++){
                    printf (" ");
                }
                gotoxy (30,(5+line));

                for (i = 0; i < counter; i++)
                    printf("%c", b[i]);

                SetColor(9);
                SetColor(12);

                for (i = counter; i < (counter + 1); i++)
                    printf("%c", b[i]);

                SetColor(9);


                for (i = (counter + 1); i < length; i++)
                    printf("%c", b[i]);
            }
            start2 = clock();
        }
            if (key == 0){
                end2 = clock();
                double wasted_time = (double)(end2 - start2) / CLOCKS_PER_SEC;
                if(wasted_time >= abs((3.5 - (level/5)))){
                    gotoxy (30, (5+line));
                    for (i=0; i< 20; i++){
                        printf (" ");
                    }
                    gotoxy (30, (line +6));
                    line++;
                    if (check_color == 1){
                        for (i = 0; i < counter; i++)
                            printf("%c", b[i]);

                        SetColor(10);

                        for (i = counter; i < (counter + 1); i++)
                            printf("%c", b[i]);

                        SetColor(9);

                        for (i = (counter + 1); i < length; i++)
                            printf("%c", b[i]);

                    }
                    else if (check_color == -1){
                        for (i = 0; i < counter; i++)
                            printf("%c", b[i]);

                        SetColor(9);
                        SetColor(12);

                        for (i = counter; i < (counter + 1); i++)
                            printf("%c", b[i]);
                            SetColor(9);

                        for (i = (counter + 1); i < length; i++)
                            printf("%c", b[i]);
                    }
                    else{
                        for (i = 0; i < length; i++)
                        printf("%c", b[i]);
                        }
                    start2 = clock();
                }
            }
        }
        gotoxy (30, (5+line));
        for (i=0; i< 20; i++){
            printf (" ");
        }


    clock_t end = clock ();
    time_spent = (double)(end - start) / CLOCKS_PER_SEC - tmp_time;
    word_score = (double)((length*3) - fault) / time_spent;
    total_time += (time_spent)/60;

    return 1;
}

void show_list(struct node **list)
{
    char choice;
    int i,n;
    level_score[0] = 0;
    level_score[1] = 0;
    level_score[2] = 0;
    diagram ();
    for(i=0; i<size; i++){
        n=show(&*list);
       // system("cls");
        if (n == 0){
            system("cls");
            level_score[0] /= (i + 1);
            total_score[0] += level_score[0];
            level_score[1] = (words / total_time);
            level_score[2] = (chars / total_time);
            total_score[1] = level_score[1];
            total_score[2] = level_score[2];

            printf(":-( Exit?!\n\nSave current state?[Y/N]\n\n");
            //printf("words number= %d, chars number %d, total time= %f\n\n", words, chars, total_time);
            printf("This level Score :\n %f\n\nTotal Score:\n %f\n %f WPM\n %f CPM\n\n", level_score[0], total_score[0], total_score[1], total_score[2]);
            scanf (" %c", &choice);
            if (choice == 'Y'){
                save ();
                system ("cls");
                printf ("Your game saved! :)\n");
            }
            exit(0);
        }
    level_score[0] +=word_score;
    }
    level_score[0] /= size;
    level_score[1] = (words / total_time);
    level_score[2] = (chars / total_time);

}

/*************************************************
                  levels
*************************************************/

void welcome()
{
    FileNumber();
    char Name[20], choice;
    FILE *my_file;

    printf("Welcome!\n\nuser name: ");
    gets (Name);
    name = Name;
    system ("cls");
    find_user();
    printf ("Hi %s R U ready to play? :-)\n\n[1]Start a new game\n\n[2]Resume an old game\n\n[3]Top 10 players\n\n", Name);
    scanf (" %c", &choice);
    system ("cls");

    if (choice == '3'){
        system ("cls");
        printf ("Choose your scoring system :D:\n\n[1] Normal\t[2] WPN(words per minute)\t[3] CPN(characters per minute)\n\n");
        scanf ("%d", &scoreset);
        TopUsers ();
        exit (0);
    }

    if (choice == '1'){
        printf("Okay, let's start\n\nPlease Enter the Level (now, I have at most %d levels): ", file_number);
        scanf (" %d", &level);
        system ("cls");
    }

    if( choice == '2' ){
        if (counter == -1){
            printf("Sry :-( this user does't exist\n");
            exit (0);
        }
        if (player.user_level == (file_number + 1)){
            printf("Sry :-( this user does't exist\n");
            exit (0);
        }
        level = player.user_level;
        total_score[0] = player.user_score[0];
        total_score[1] = player.user_score[1];
        total_score[2] = player.user_score[2];

    }
    char file_name1[100] = "level-";
    char file_level[100];
    char file_name2[100] = ".txt";
    sprintf (file_level, "%d", level);
    strcat (file_name1,file_level);
    strcat (file_name1,file_name2);
    my_file = fopen (file_name1, "r");

    if (my_file == NULL){
        printf ("Invalid input");
        return 0;
    }


    run_level (my_file);

    while(1){
        if (level == file_number ){
            line = 0;
            level++;
            save ();
            printf ("Game is over!\n\n");
            printf("This level Score :\n %f\n\nTotal Score:\n %f\n %f WPM\n %f CPM\n\n", level_score[0], total_score[0], total_score[1], total_score[2]);
            save();
            exit (0);
        }
        printf("This level Score :\n %f\n\nTotal Score:\n %f\n %f WPM\n %f CPM\n\n", level_score[0], total_score[0], total_score[1], total_score[2]);
        printf("Do U want to go to the next level? [Y/N]\n\n");
        scanf (" %c", &choice);
        system ("cls");
        if(choice == 'Y'){
            line = 0;
            level++;
            next_level(level);
        }
        else if (choice == 'N'){
            line = 0;
            level++;
            printf ("Do U want to save the game?[Y/N]\n\n");
            scanf (" %c", &choice);
            if (choice == 'Y'){
                save ();
                system ("cls");
                printf ("Your game saved! :)\n");
                }
            exit(0);
        }
        else{
            system ("cls");
            printf ("Invalid input\n");
            exit (0);
        }
    }

}

void run_level(FILE *my_file)
{

    struct node *my_list;
    my_list = create_set( my_file);
    show_list (&my_list);
    system ("cls");
    total_score[0] += level_score[0];
    total_score[1] = level_score[1];
    total_score[2] = level_score[2];

}


void next_level(int level)
{
    FILE *my_file;
    char file_name1[100] = "level-";
    char file_level[100];
    char file_name2[100] = ".txt";
    sprintf (file_level, "%d", level);
    strcat (file_name1,file_level);
    strcat (file_name1,file_name2);
    my_file = fopen (file_name1, "r");

    if (my_file == NULL){
        printf ("Invalid level");
        return 0;
    }
    run_level (my_file);
}

/*************************************************
                   Main
*************************************************/


int main(void)
{
    system("color F9");
    welcome ();
    return 0;
}
