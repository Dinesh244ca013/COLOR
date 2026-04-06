
// FLOOD FILLING 

#include <iostream>
#include <GL/glut.h>

using namespace std;
float  nc[3]={1,0,0};
float  oc[3]={0,0,0};
float  bound[3]={1,1,1};

void floodfill(float x , float y , float  oc[3] , float nc[3])
{
    float c_color[3];
    glReadPixels(x ,y , 1, 1, GL_RGB , GL_FLOAT , c_color);
    
    if( (c_color[0]==oc[0] && c_color[1]==oc[1] && c_color[2]==oc[2]) && (c_color[0]!=bound[0] && c_color[1]!=bound[1] && c_color[2]!=bound[2]))
    {
          glColor3f(1,0,0);
          glBegin(GL_POINTS);
             glVertex2f(x,y);
          glEnd();
          glFlush();
          
          floodfill(x+1 , y, oc , nc);
          floodfill(x-1 , y, oc , nc);
          floodfill(x , y+1, oc , nc);
          floodfill(x , y-1, oc , nc);
    }

}

void mouse(int button , int state , int x , int y)
{
     if(button==GLUT_LEFT_BUTTON && state==GLUT_DOWN){
             float xi=x;
             float yi=500-y;  
             float clickcolor[3];
             glReadPixels(xi,yi,1,1,GL_RGB , GL_FLOAT , clickcolor);
             
             if(clickcolor[0]==bound[0] && clickcolor[1]==bound[1] && clickcolor[2]==bound[2])
             {
                   cout<<"you are click the boundary position "<<endl; 
             }else{
                 
                  floodfill( xi ,yi, oc , nc );
             }
     }
}
void display()
{
     glClear(GL_COLOR_BUFFER_BIT);
     glColor3fv(bound);
     glBegin(GL_LINE_LOOP);
        glVertex2f(150,150);
        glVertex2f(350,150);
        glVertex2f(350,350);
        glVertex2f(150,350);
     glEnd();
     glFlush();
}
void init()
{
     glClearColor(0,0,0,0);
     glEnable(GL_DEPTH_TEST);
     glMatrixMode(GL_PROJECTION);
     glLoadIdentity();
     gluOrtho2D(0,500,0,500);
     glMatrixMode(GL_MODELVIEW);
}
int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutInitWindowSize(500, 500);
    glutCreateWindow("Fixed Flood Fill");
    init();
    glutDisplayFunc(display);
    glutMouseFunc(mouse);
    glutMainLoop();
    return 0;
}

// BOUNDARY FILL

#include <iostream>
#include <GL/glut.h>

using namespace std;

// Colors define karein
float f_col[3] = {1.0, 0.0, 0.0}; // Fill Color (Red)
float b_col[3] = {1.0, 1.0, 1.0}; // Boundary Color (White)

void boundaryfill8(int x, int y, float f_color[3], float b_color[3]) {
    float c_color[3];
    
    // 1. Current pixel ka color read karein
    glReadPixels(x, y, 1, 1, GL_RGB, GL_FLOAT, c_color);

  
    if ((c_color[0] != b_color[0] || c_color[1] != b_color[1] || c_color[2] != b_color[2]) &&
        (c_color[0] != f_color[0] || c_color[1] != f_color[1] || c_color[2] != f_color[2])) 
    {
        // Pixel draw karein
        glColor3fv(f_color);
        glBegin(GL_POINTS);
            glVertex2i(x, y);
        glEnd();
        glFlush();

        // 8-Connected Recursion (All 8 directions)
        boundaryfill8(x + 1, y, f_color, b_color);     // Right
        boundaryfill8(x - 1, y, f_color, b_color);     // Left
        boundaryfill8(x, y + 1, f_color, b_color);     // Up
        boundaryfill8(x, y - 1, f_color, b_color);     // Down
        
        // Diagonals (Tirche corners)
        boundaryfill8(x + 1, y + 1, f_color, b_color); // Top-Right
        boundaryfill8(x - 1, y + 1, f_color, b_color); // Top-Left
        boundaryfill8(x + 1, y - 1, f_color, b_color); // Bottom-Right
        boundaryfill8(x - 1, y - 1, f_color, b_color); // Bottom-Left
    }
}

void mouse(int button, int state, int x, int y) {
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN) {
        int xi = x;
        int yi = 500 - y; // Coordinate correction
        
        cout << "8-Connected Fill started at: " << xi << ", " << yi << endl;
        boundaryfill8(xi, yi, f_col, b_col);
    }
}

void display() {
    glClear(GL_COLOR_BUFFER_BIT);

    // Boundary Draw karein (Chota Rectangle taaki crash na ho)
    glColor3fv(b_col);
    glLineWidth(2);
    glBegin(GL_LINE_LOOP);
        glVertex2i(200, 200);
        glVertex2i(250, 200);
        glVertex2i(250, 250);
        glVertex2i(200, 250);
    glEnd();

    glFlush();
}

void init() {
    glClearColor(0.0, 0.0, 0.0, 1.0); // Background Black
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, 500, 0, 500);
}

int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutInitWindowSize(500, 500);
    glutCreateWindow("Boundary Fill 8-Connected Logic");

    init();
    glutDisplayFunc(display);
    glutMouseFunc(mouse);
    
    glutMainLoop();
    return 0;
}


