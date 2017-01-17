# -
用C语言编写的一个对话控制台游戏。程序中用到结构体，函数，循环，选择，判断，结构体，指针等C语言基础知识。

# include <stdio.h>
# include <time.h>
# include <stdlib.h>


struct Person
{
	char name[20];
	int blood;
	int hunger;
	int thirsty;
	int strength;
};

void Character_Information(struct Person * pPerson);
void Character_Explore(struct Person * pPerson, int ** pSystem_Time);
void Character_Rest(struct Person * pPerson, int ** pSystem_Time) ;
void Game_Rule(void);
int Number_Rand_blood(void);
int Number_Rand_hunger(void);
int Number_Rand_thirsty(void);
int Number_Rand_strength(void);
void Enter_Continue(void);
void Author (void);
void Game_Story(struct Person * pPerson);
void Character_Choice(struct Person * pPerson, int * pSystem_Time);

int main(void)
{
	struct Person character;
	struct Person * pPerson = &character;
	int time = 0;
	
	//初始化人物信息
	pPerson->blood = 60;
	pPerson->hunger = 10;
	pPerson->thirsty = 10;
	pPerson->strength = 20;
	
	Author();  //作者声明
	Game_Rule();   //游戏规则
	Game_Story(pPerson);  //剧情
	
	return 0;
}

void Game_Story(struct Person * pPerson)
{
	int i;
	int System_Time = 0;
	int * pSystem_Time = &System_Time;

	printf("故事开始!!!\n\n");
	printf("请输入你的名字：");
	scanf("%s",pPerson->name);
	printf("\n欢迎%s来到沙漠求生!!\n\n",pPerson->name);
	Character_Information(pPerson);  //输出人物信息
	
	while (pPerson->blood > 0 || pPerson->strength > 0 || pPerson->thirsty > 0 || pPerson->hunger > 0)
	{
		Character_Choice(pPerson, pSystem_Time);   // 用户选择操作
		printf("时间经过了%d个小时\n", System_Time);
		for (i=1;i<10;i++)
		{
			if (System_Time/24 == i)
			{
				printf("------------------\n");	
				printf("恭喜你通过了第%d天!!!\n", System_Time/24);
				printf("------------------\n");	
			}
		}
	}
	
	return;
}

void Character_Choice(struct Person * pPerson, int * pSystem_Time)  //用户选择操作
{
	int choice;
		
	printf("你有以下选择：\n");
	printf("1.探索\n");
	printf("2.原地休息\n");
	scanf("%d", &choice);	
	switch(choice)
	{
		case 1:
			Character_Explore(pPerson, &pSystem_Time);
			break;
		case 2:
			Character_Rest(pPerson,&pSystem_Time);
			break;
		default:
			system("cls");
			printf("选择错误！请重新选择！\n");
			break;
	}
	Character_Information(pPerson);  //输出人物信息
	if (pPerson->blood <= 0 || pPerson->strength <= 0 || pPerson->thirsty <= 0 || pPerson->hunger <= 0)
	{
		system("cls");
		printf("Game Over!\n");
		exit(-1); //终止程序
	}
	
	return;
}

void Character_Information(struct Person * pPerson)  //输出人物信息
{
	printf("---------------------------\n");
	printf("你现在的情况是这样：\n");
	printf("    血量：%d\n",pPerson->blood);
	printf("    体力：%d\n",pPerson->strength);
	printf("    饥饿度：%d\n",pPerson->hunger);
	printf("    饥渴度：%d\n",pPerson->thirsty);
	printf("---------------------------\n");
}

void Character_Explore(struct Person * pPerson, int ** pSystem_Time)  //探索
{
	pPerson->blood -= Number_Rand_blood();
	pPerson->strength -= Number_Rand_strength();
	pPerson->hunger += Number_Rand_hunger();
	pPerson->thirsty += Number_Rand_thirsty();
	system("cls");
	
	printf("\n你选择的是探索，不幸遇到猛兽，血量减少；但是找到食物和水，饥渴度恢复\n");
	printf("--------------------------------------------------------------------\n");
	printf("你的blood减少%d，你的strength减少%d，你的hunger增加%d，你的thirsty增加%d\n\n", Number_Rand_blood(), Number_Rand_strength(), Number_Rand_hunger(), Number_Rand_thirsty());
	**pSystem_Time += 6;  //每次探索耗时6个小时
}

void Character_Rest(struct Person * pPerson, int ** pSystem_Time)    // 休息
{
	int rest_time = 0;
	printf("请输入休息时长（小时）：");
	scanf("%d", &rest_time);
	pPerson->hunger -= rest_time;
	pPerson->thirsty -= rest_time;
	pPerson->strength += rest_time;
	system("cls");
	//printf("第%d天\n", **pSystem_Time/24);
	printf("\n你的strength恢复%d，你的hunger减少%d，你的thirsty减少%d\n\n", rest_time, rest_time, rest_time);
	**pSystem_Time += rest_time;
}

void Game_Rule(void)  //游戏规则
{
	printf("\n\n-----------------------------------------------------------------------\n");
	printf("游戏规则：\n");
	printf("\n人物初始信息：\n");
	printf("	*blood = 60\n");
	printf("	*hunger = 10\n");
	printf("	*thirsty = 10\n");
	printf("	*strength = 20\n");
	printf("\n你可以选择探索或则休息:\n\n");
	printf("探索：\n");
	printf("    每次探索会随机增加饥饿和口渴度，但会随机消耗血量和体力\n");
	printf("休息：\n");
	printf("    每次休息会恢复体力和血量（1/h）,但会减少饥饿度和口渴度(1/h)\n\n");
	printf("任一指标至0点，即游戏结束!\n");
	printf("------------------------------------------------------------------------\n\n");
	Enter_Continue();
}

int Number_Rand_blood(void)  //随机数_血量
{
	int number;
	
	srand((unsigned) time(NULL)); //用时间做种，每次产生随机数不一样
	number = rand() % 6 + 3;  //产生3-8的随机数
	
	return number;
}

int Number_Rand_hunger(void)  //随机数_饥饿
{
	int number;
	
	srand((unsigned) time(NULL)); //用时间做种，每次产生随机数不一样
	number = rand() % 4 + 2;  //产生2-6的随机数
	
	return number;
}

int Number_Rand_thirsty(void)  //随机数_口渴
{
	int number;
	
	srand((unsigned) time(NULL)); //用时间做种，每次产生随机数不一样
	number = rand() % 5 + 1;  //产生1-5的随机数
	
	return number;
}

int Number_Rand_strength(void)  //随机数_体力
{
	int number;
	
	srand((unsigned) time(NULL)); //用时间做种，每次产生随机数不一样
	number = rand() % 3 + 5;  //产生5-7的随机数
	
	return number;
}

void Enter_Continue(void)  //清屏和回车继续
{
	printf("请按Enter键继续！！");
	/*
	while(getchar()!='Y')
		continue;
	system("cls");
	printf("游戏继续\n");
	*/
	if(getchar()=='\n')
		system("cls");
    return;
}

void Author (void)   //作者声明
{
	printf("\n-------------------------\n\n");
	printf("《沙漠求生》—Tao\n\n");
	printf("-------------------------\n");
	return;
}
