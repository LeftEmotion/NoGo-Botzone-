// 不围棋（NoGo）样例程序
// 随机策略
// 游戏信息：http://www.botzone.org/games#NoGo


//json交互
#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>
#include<algorithm>
#include "jsoncpp/json.h"
using namespace std;

int board[9][9] = { {0} };


bool dfs_air_visit[9][9];
const int cx[] = { -1,0,1,0 };
const int cy[] = { 0,-1,0,1 };
const int inf = 1<<30;
bool inBorder(int x, int y) { return x >= 0 && y >= 0 && x<9 && y<9; }

//true: has air
bool dfs_air(int fx, int fy)
{
	dfs_air_visit[fx][fy] = true;
	bool flag = false;
	for (int dir = 0; dir < 4; dir++)
	{
		int dx = fx + cx[dir], dy = fy + cy[dir];
		if (inBorder(dx, dy))//不越界 
		{
			if (board[dx][dy] == 0)//找到空位，说明有气，返回1 
				flag = true;
			if (board[dx][dy] == board[fx][fy] && !dfs_air_visit[dx][dy])//同色，且未被访问，递归该点 
				if (dfs_air(dx, dy))//如果该点有气，说明原来点也有气，返回1 
					flag = true; 
		}
	}
	return flag;
}

//true: available
int judgeAvailable(int fx, int fy, int col)
{
	if (board[fx][fy]) return false;//若此处已放棋子，则决不能再放棋子
	board[fx][fy] = col;
	memset(dfs_air_visit, 0, sizeof(dfs_air_visit));
	if (!dfs_air(fx, fy))//无气，空位置 
	{
		board[fx][fy] = 0;
		return 0;
	}
	for (int dir = 0; dir < 4; dir++)
	{
		int dx = fx + cx[dir], dy = fy + cy[dir];
		if (inBorder(dx, dy))
		{
			if (board[dx][dy] && !dfs_air_visit[dx][dy])
				if (!dfs_air(dx, dy))
				{
					board[fx][fy] = 0;
					return false;
				}
		}
	}
	board[fx][fy] = 0;
	return true;
}
int judgeAir(int fx, int fy ,int col){
	board[fx][fy] = col;
	memset(dfs_air_visit, 0, sizeof(dfs_air_visit));
	if (!dfs_air(fx, fy))//无气，空位置 
	{
		board[fx][fy] = 0;
		return 0;
	}
	for (int dir = 0; dir < 4; dir++)
	{
		int dx = fx + cx[dir], dy = fy + cy[dir];
		if (inBorder(dx, dy))
		{
			if (board[dx][dy] && !dfs_air_visit[dx][dy])
				if (!dfs_air(dx, dy))
				{
					board[fx][fy] = 0;
					return false;
				}
		}
	}
	board[fx][fy] = 0;
	return true;
}
/*
board[x][y] == 0 为空
			== 1  为对方
			== -1  自己 

*/
/*
评估 自己能下的点 减去 对方能下的点 找最大值 

*/
int judgeeye(int col){//传入颜色
	int num = 0;
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,col) && !judgeAvailable(i,j,-col))
				num++;
		}
	return num;
}
int judgehalfeye(int col){
	int prelist = 0;
	int behlist = 0;
	int num = 0;//眼数返回值 
	prelist = judgeeye(col);
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,col)){
				board[i][j] = col;
				behlist = judgeeye(col);
				if(behlist > prelist)//眼变多
					num += behlist - prelist;
				board[i][j] = 0;
			}
		}
	return num;
}
void judge(int &nx, int &ny, int x){
	int mval = -100000;
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,-1)){//该点能下，假设下该点，进行预测
				int halfeye = judgehalfeye(-1);//半眼
				int enemyhalfeye = judgehalfeye(1);//敌方半眼
				int eye = judgeeye(-1);
				int enemyeye = judgeeye(1);
				/**///开始
				board[i][j] = -1;//预测 
				int my = 0;
					//全眼更多
					my += (judgeeye(-1) - eye)* 30;
					//半眼更多
					my += (judgehalfeye(-1) - halfeye) * 12;
					//敌方全眼或半眼变少
					board[i][j] = 1;
					my += (enemyeye - judgeeye(1)) * 18;
					my += (enemyhalfeye - judgehalfeye(1)) * 5;
				board[i][j] = 0; 
				if(my > mval){//更新 
					nx = i;
					ny = j;
					mval = my;
				}	
			}
		}
}

int getValue(){//估值函数： 我方可下减去对方可下
	int ans=0;
	for(int i=0;i<9;i++){
		for(int j=0;j<9;j++){
			ans+=judgeAvailable(i,j,-1)-judgeAvailable(i,j,1);
		}
	}
	return ans;
}

int Final(int col){//判断是否终局
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,col)){
				return 0;
			}
		}
	return 1;
}
/*
int MinMax(int col, int depth){
	int a;
	if(FINAL(col) || depth == 5){//层数够深或者已经结局
		return getValue();
	}
	if(col == 1){//对手执步
		a = inf;
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,1)){
					board[i][j] = 1;
					a = min(a, MinMax(-1, depth + 1));
					board[i][j] = 0;
				}
			}
	}
	else{//我方执步
		a = -inf;
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,-1)){
					board[i][j] = -1;
					a = max(a,MinMax(1,depth+1));
					board[i][j] = 0;
				}
			}
	}
	return a;
}
*/
int MinMax(int col, int depth){
	int a;
	if(Final(col)||Final(-col) || depth == 3){//层数够深或者已经结局
		return col*getValue();
	}
	a=-inf;
	for(int i=0;i<9;i++){
		for(int j=0;j<9;j++){
			if(judgeAvailable(i,j,col)){
				board[i][j]=1;
				a=max(a,MinMax(-col,depth+1));
				board[i][j]=0;
			}
		}
	}

	return -a;
}
/*
int AlphaBeta(int col, int depth, int &na, int &nb){
	int score;
	if(depth == 5 || Final(col)||Final(-col)){
		return getValue();
	}
	if(col == 1){
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,1)){
					board[i][j] = 1;
					score = AlphaBeta(-1, depth+1, na, nb);
					board[i][j] = 0;
					if(score < nb){
						nb = score;
						if(na >= nb){
							return na;
						}
					}
				}
			}
		return nb;
	}
	else{//我方执步，找极大值
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,-1)){
					board[i][j] = -1;
					score =  AlphaBeta(1, depth+1, na, nb);
					board[i][j] = 0;
					if(score > na){
						na = score;
						if(na >= nb){
							return nb;
						}
					}
				}
			}
		return na;
	}
}*/
/*
int AlphaBeta(int col, int depth, int &na, int &nb){
	int score;
	if(depth == 3 || Final(col)||Final(-col)){
		return (-col)*getValue();
	}

	for(int i=0;i<9;i++){
		for(int j=0;j<9;j++){
			if(judgeAvailable(i,j,col)){
				board[i][j]=col;
				score=AlphaBeta(-col,depth+1,na,nb);
				board[i][j]=0;
				if()
			}
		}
	}

	if(col == 1){
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,1)){
					board[i][j] = 1;
					score = AlphaBeta(-1, depth+1, na, nb);
					board[i][j] = 0;
					if(score < nb){
						nb = score;
						if(na >= nb){
							return na;
						}
					}
				}
			}
		return nb;
	}
	else{//我方执步，找极大值
		for(int i = 0;i < 9;i++)
			for(int j = 0;j < 9;j++){
				if(judgeAvailable(i,j,-1)){
					board[i][j] = -1;
					score =  AlphaBeta(1, depth+1, na, nb);
					board[i][j] = 0;
					if(score > na){
						na = score;
						if(na >= nb){
							return nb;
						}
					}
				}
			}
		return na;
	}
}
*/
/*
int SecondDo(int &nx, int &ny){
	int Maxval = -inf;
	int a = -inf;
	int b = inf;
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,-1)){
				board[i][j] = -1;
				int tem = AlphaBeta(1,1,a,b);
				board[i][j] = 0;
				if(tem > Maxval){
					nx = i;
					ny = j;
					Maxval = tem;
				}
			}
		}
		return Maxval;
}*/

int SecondDo(int &nx, int &ny){
	int Maxval = -inf;
	int a = -inf;
	int b = inf;
	for(int i = 0;i < 9;i++)
		for(int j = 0;j < 9;j++){
			if(judgeAvailable(i,j,-1)){
				board[i][j] = -1;
				int tem = MinMax(1,1);
				board[i][j] = 0;
				if(tem > Maxval){
					nx = i;
					ny = j;
					Maxval = tem;
				}
			}
		}
		return Maxval;
}

bool a(int &new_x,int&new_y){
	if (board[0][0] == 0) {
		new_x = 0;
		new_y = 0;
		return 1;
	}
	else if (board[8][0] == 0) {
		new_x = 8;
		new_y = 0;
		return 1;
	}
	else if (board[0][8] == 0) {
		new_x = 0;
		new_y = 8;
		return 1;
	}
	else if (board[8][8] == 0) {
		new_x = 8;
		new_y = 8;
		return 1;
	}
	return false;
}


int main()
{
	srand((unsigned)time(0));
	string str;
	int x, y;
	// 读入JSON
	getline(cin, str);
	//getline(cin, str);
	Json::Reader reader;
	Json::Value input;
	reader.parse(str, input);
	// 分析自己收到的输入和自己过往的输出，并恢复棋盘状态
	int turnID = input["responses"].size();//第一步时turnID等于0，注意是responses的size，而不是request的size
	for (int i = 0; i < turnID; i++)
	{
		x = input["requests"][i]["x"].asInt(), y = input["requests"][i]["y"].asInt();//对方，注意此处是requests
		if (x != -1) board[x][y] = 1;
		x = input["responses"][i]["x"].asInt(), y = input["responses"][i]["y"].asInt();//我方，注意此处是responses
		if (x != -1) board[x][y] = -1;
	}
	x = input["requests"][turnID]["x"].asInt(), y = input["requests"][turnID]["y"].asInt();//对方，注意此处是requests
	if (x != -1) board[x][y] = 1;
	//此时board[][]里存储的就是当前棋盘的所有棋子信息,x和y存的是对方最近一步下的棋
	
	/************************************************************************************/
	/***********在下面填充你的代码，决策结果（本方将落子的位置）存入new_x和new_y中****************/
		//下面仅为随机策略的示例代码，可删除
	/*int available_list[81]; //合法位置表 
	int k = 0;
	for (int i = 0; i<9; i++)
		for (int j = 0; j<9; j++)
			if (judgeAvailable(i, j, x == -1 ? 1 : -1))
			{
				available_list[k] = i * 9 + j;
				k++;
			}
	int result = available_list[rand() % k];
	int new_x = result / 9;		int new_y = result % 9;*/
	int new_x;
	int dbg;
	int new_y;
	int flag = 1;
	if(a(new_x,new_y)){}
	else if(flag && turnID < 20)
		judge(new_x,new_y,x);
	else if(flag && turnID >= 20){
		dbg = SecondDo(new_x, new_y);
	}
	/***********在上方填充你的代码，决策结果（本方将落子的位置）存入new_x和new_y中****************/
	/************************************************************************************/
	
	// 输出决策JSON
	Json::Value ret;
	Json::Value action;
	action["x"] = new_x; action["y"] = new_y;
	ret["response"] = action;
	ret["debug"] = dbg;//调试信息可写在这里
	Json::FastWriter writer;

	cout << writer.write(ret) << endl;
	return 0;
}
