# A-PathFinding-Algo
#include<iostream>
#include<set>
#include<cmath>
#include<climits>
#include<stack>
using namespace std;
#define ROW 5
#define COL 5
typedef pair<int,int> Pair;
typedef pair<int,pair<int,int> >mulPair;
class cell
{
    public:
    //parent of the particular cell
        int parent_x,parent_y;
        //f=g+h
        double f,g,h;
};
bool isValid(int row,int col)
{
    return (row>=0)&&(row<ROW)&&(col>=0)&&(col<COL);
}
bool isUnBlocked(char grid[][COL],int row,int col)
{
    if(grid[row][col]=='*')
    {
        return false;
    }
    else
    {
        return true;
    }
    
}
bool isDestination(int row,int col, Pair dest)
{
    if(row==dest.first&&col==dest.second)
    {
        return true;
    }
    return false;
}
double calculateHValue(int row,int col,Pair dest)
{
    return ((double)sqrt((row-dest.first)*(row-dest.first)+(col-dest.second)*(col-dest.second)));
}
void tracePath(cell cellDetails[][COL],Pair dest,char grid[][COL])
{
    int row=dest.first;
    int col=dest.second;
    stack<Pair>Path;
    while(!(cellDetails[row][col].parent_x==row&&cellDetails[row][col].parent_y==col))
    {
        Path.push(make_pair(row,col));
        int temp_row=cellDetails[row][col].parent_x;
        int temp_col=cellDetails[row][col].parent_y;
        row=temp_row;
        col=temp_col;
    }
    Path.push(make_pair(row,col));
    while(!Path.empty())
    {
        Pair p=Path.top();
        Path.pop();
        cout<<"( "<<p.first<<", "<<p.second<<" )"<<"-->";
        //"1" trace the shortest path from source to destination
        grid[p.first][p.second]='1';
    }
    cout<<endl;
}
void aStarSearch(char grid[][COL],Pair src,Pair dest)
{
    if(isValid(src.first, src.second)==false)
    {
        cout<<"source invalid"<<endl;
        return;
    }
    if(isValid(dest.first,dest.second)==false)
    {
        cout<<"destination invalid"<<endl;
        return;
    }
    if(!isUnBlocked(grid,src.first,src.second)||!isUnBlocked(grid,dest.first,dest.second))
    {
        cout<<"Either the source or the destination or both is(are) blocked!"<<endl;
        return;
    }
    if(isDestination(src.first, src.second, dest)==true)
    {
        cout<<"We are already at destination!\n";
        return;
    }

    //formation of your closed List-->
    bool closedList[ROW][COL];
    memset(closedList,false,sizeof(closedList));
    cell cellDetails[ROW][COL];
    int i,j;
    //Adding all the cell details
    //in the 2D object array;
    for(i=0;i<ROW;i++)
    {
        for(j=0;j<COL;j++)
        {
            cellDetails[i][j].f=cellDetails[i][j].g=cellDetails[i][j].h=INT_MAX;
            cellDetails[i][j].parent_x=-1;
            cellDetails[i][j].parent_y=-1;   
        }
    }
    //for the source specifically
    i=src.first;
    j=src.second;
    cellDetails[i][j].f=cellDetails[i][j].g=cellDetails[i][j].h=0.0;
    cellDetails[i][j].parent_x=i;
    cellDetails[i][j].parent_y=j;
    //formation of your open List-->
    //set<f,pair<x,y> >
    set<mulPair>openList;
    openList.insert(make_pair(0.0,make_pair(i,j)));
    bool foundDest=false;
    while(!openList.empty())
    {
        mulPair p=*openList.begin();
        openList.erase(openList.begin());
        i=p.second.first;
        j=p.second.second;
        closedList[i][j]=true;
        double gNew,hNew,fNew;
        //Generating all the possible directions-->
        //North
        if(isValid(i-1,j)==true)
        {
            if(isDestination(i-1,j,dest)==true)
            {
                cellDetails[i-1][j].parent_x=i;
                cellDetails[i-1][j].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i-1][j]==false&&isUnBlocked(grid,i-1,j)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i-1,j,dest);
                fNew=gNew+hNew;
                if(cellDetails[i-1][j].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i-1,j)));
                    cellDetails[i-1][j].f=fNew;
                    cellDetails[i-1][j].g=gNew;
                    cellDetails[i-1][j].h=hNew;
                    cellDetails[i-1][j].parent_x=i;
                    cellDetails[i-1][j].parent_y=j;
                }
            }
        }
        //South
        if(isValid(i+1,j)==true)
        {
            if(isDestination(i+1,j,dest)==true)
            {
                cellDetails[i+1][j].parent_x=i;
                cellDetails[i+1][j].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i+1][j]==false&&isUnBlocked(grid,i+1,j)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i+1,j,dest);
                fNew=gNew+hNew;
                if(cellDetails[i+1][j].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i+1,j)));
                    cellDetails[i+1][j].f=fNew;
                    cellDetails[i+1][j].g=gNew;
                    cellDetails[i+1][j].h=hNew;
                    cellDetails[i+1][j].parent_x=i;
                    cellDetails[i+1][j].parent_y=j;
                }
            }
        }
        //East
        if(isValid(i,j+1)==true)
        {
            if(isDestination(i,j+1,dest)==true)
            {
                cellDetails[i][j+1].parent_x=i;
                cellDetails[i][j+1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i][j+1]==false&&isUnBlocked(grid,i,j+1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i,j+1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i][j+1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i,j+1)));
                    cellDetails[i][j+1].f=fNew;
                    cellDetails[i][j+1].g=gNew;
                    cellDetails[i][j+1].h=hNew;
                    cellDetails[i][j+1].parent_x=i;
                    cellDetails[i][j+1].parent_y=j;
                }
            }
        }
        //West
        if(isValid(i,j-1)==true)
        {
            if(isDestination(i,j-1,dest)==true)
            {
                cellDetails[i][j-1].parent_x=i;
                cellDetails[i][j-1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i][j-1]==false&&isUnBlocked(grid,i,j-1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i,j-1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i][j-1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i,j-1)));
                    cellDetails[i][j-1].f=fNew;
                    cellDetails[i][j-1].g=gNew;
                    cellDetails[i][j-1].h=hNew;
                    cellDetails[i][j-1].parent_x=i;
                    cellDetails[i][j-1].parent_y=j;
                }
            }
        }
        //North-east
        if(isValid(i-1,j+1)==true)
        {
            if(isDestination(i-1,j+1,dest)==true)
            {
                cellDetails[i-1][j+1].parent_x=i;
                cellDetails[i-1][j+1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i-1][j+1]==false&&isUnBlocked(grid,i-1,j+1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i-1,j+1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i-1][j+1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i-1,j+1)));
                    cellDetails[i-1][j+1].f=fNew;
                    cellDetails[i-1][j+1].g=gNew;
                    cellDetails[i-1][j+1].h=hNew;
                    cellDetails[i-1][j+1].parent_x=i;
                    cellDetails[i-1][j+1].parent_y=j;
                }
            }
        }
        //North-West
        if(isValid(i-1,j-1)==true)
        {
            if(isDestination(i-1,j-1,dest)==true)
            {
                cellDetails[i-1][j-1].parent_x=i;
                cellDetails[i-1][j-1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i-1][j-1]==false&&isUnBlocked(grid,i-1,j-1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i-1,j-1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i-1][j-1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i-1,j-1)));
                    cellDetails[i-1][j-1].f=fNew;
                    cellDetails[i-1][j-1].g=gNew;
                    cellDetails[i-1][j-1].h=hNew;
                    cellDetails[i-1][j-1].parent_x=i;
                    cellDetails[i-1][j-1].parent_y=j;
                }
            }
        }
        //South-east
        if(isValid(i+1,j+1)==true)
        {
            if(isDestination(i+1,j+1,dest)==true)
            {
                cellDetails[i+1][j+1].parent_x=i;
                cellDetails[i+1][j+1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i+1][j+1]==false&&isUnBlocked(grid,i+1,j+1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i+1,j+1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i+1][j+1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i+1,j+1)));
                    cellDetails[i+1][j+1].f=fNew;
                    cellDetails[i+1][j+1].g=gNew;
                    cellDetails[i+1][j+1].h=hNew;
                    cellDetails[i+1][j+1].parent_x=i;
                    cellDetails[i+1][j+1].parent_y=j;
                }
            }
        }
        //South-West
        if(isValid(i+1,j-1)==true)
        {
            if(isDestination(i+1,j-1,dest)==true)
            {
                cellDetails[i+1][j-1].parent_x=i;
                cellDetails[i+1][j-1].parent_y=j;
                cout<<"The destination cell is found:\n";
                tracePath(cellDetails,dest,grid);
                foundDest=true;
                return;
            }
            else if(closedList[i+1][j-1]==false&&isUnBlocked(grid,i+1,j-1)==true)
            {
                gNew=cellDetails[i][j].g+1.0;
                hNew=calculateHValue(i+1,j-1,dest);
                fNew=gNew+hNew;
                if(cellDetails[i+1][j-1].f==INT_MAX||cellDetails[i][j].f>fNew)
                {
                    openList.insert(make_pair(fNew,make_pair(i+1,j-1)));
                    cellDetails[i+1][j-1].f=fNew;
                    cellDetails[i+1][j-1].g=gNew;
                    cellDetails[i+1][j-1].h=hNew;
                    cellDetails[i+1][j-1].parent_x=i;
                    cellDetails[i+1][j-1].parent_y=j;
                }
            }
        }
    }
    if(foundDest==false)
    {
        cout<<"Failed to find the destination!\n";
    }
    return;

}
void displayGrid(char grid[][COL])
{
    for(int i=0;i<ROW;i++)
    {
        for(int j=0;j<COL;j++)
        {
            cout<<grid[i][j];
        }
        cout<<endl;
    }
}
int main()
{
    char grid[ROW][COL]=
    {
        {'_','_','_','_','*'},
        {'_','*','*','_','_'},
        {'_','_','_','*','_'},
        {'_','*','_','_','_'},
        {'*','*','_','*','_'}
    };
    //cout<<grid[0][4]<<endl;
    int x_src,y_src,x_dest,y_dest;
    cout<<"Enter the coordinates for source:\n";
    cin>>x_src>>y_src;
    cout<<"Enter the coordinates for source:\n";
    cin>>x_dest>>y_dest;
    Pair src=make_pair(x_src,y_src);
    Pair dest=make_pair(x_dest,y_dest);
    aStarSearch(grid,src,dest);
    displayGrid(grid);
    return 0;
}
