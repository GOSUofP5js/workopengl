## 01. Dev-CPP 사각형 큐브 대신 다른 도형 넣기

#### 코드(DEV-CPP)

    #include <windows.h>
    #include <gl/gl.h>
    #include <gl/glu.h> // GLU 헤더 파일 포함
    #include <stdlib.h>
    #include <time.h>
    
    LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);
    void EnableOpenGL(HWND hWnd, HDC *hDC, HGLRC *hRC);
    void DisableOpenGL(HWND hWnd, HDC hDC, HGLRC hRC);
    void SetupProjection(int width, int height);
    
    int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int iCmdShow)
    {
        WNDCLASS wc;
        HWND hWnd;
        HDC hDC;
        HGLRC hRC;
        MSG msg;
        BOOL bQuit = FALSE;
        float theta = 0.0f;
        float phi = 0.0f;
    
        srand(time(NULL)); // 시간에 따라 랜덤 색상을 생성하기 위해
    
        /* register window class */
        wc.style = CS_OWNDC;
        wc.lpfnWndProc = WndProc;
        wc.cbClsExtra = 0;
        wc.cbWndExtra = 0;
        wc.hInstance = hInstance;
        wc.hIcon = LoadIcon(NULL, IDI_APPLICATION);
        wc.hCursor = LoadCursor(NULL, IDC_ARROW);
        wc.hbrBackground = (HBRUSH)GetStockObject(BLACK_BRUSH);
        wc.lpszMenuName = NULL;
        wc.lpszClassName = "GLSample";
        RegisterClass(&wc);
    
        /* create main window */
        hWnd = CreateWindow("GLSample", "OpenGL Sample",
                            WS_CAPTION | WS_POPUPWINDOW | WS_VISIBLE,
                            0, 0, 512, 512,
                            NULL, NULL, hInstance, NULL);
    
        /* enable OpenGL for the window */
        EnableOpenGL(hWnd, &hDC, &hRC);
    
        /* setup projection */
        SetupProjection(512, 512);
    
        /* program main loop */
        while (!bQuit)
        {
            /* check for messages */
            if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE))
            {
                /* handle or dispatch messages */
                if (msg.message == WM_QUIT)
                {
                    bQuit = TRUE;
                }
                else
                {
                    TranslateMessage(&msg);
                    DispatchMessage(&msg);
                }
            }
            else
            {
                /* OpenGL animation code goes here */
    
                glClearColor(0.0f, 0.0f, 0.0f, 0.0f);
                glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    
                glEnable(GL_DEPTH_TEST);
                glPushMatrix();
                glRotatef(theta, 1.0f, 0.0f, 0.0f); // X축 회전
                glRotatef(phi, 0.0f, 1.0f, 0.0f);   // Y축 회전
    
                // 정사면체의 정점들
                GLfloat vertices[4][3] = {
                    {0.0f, 1.0f, 0.0f},
                    {-1.0f, -1.0f, 1.0f},
                    {1.0f, -1.0f, 1.0f},
                    {0.0f, -1.0f, -1.0f}
                };
    
                // 정사면체의 면들
                glBegin(GL_TRIANGLES);
                // 면 1
                glColor3f(1.0f, 0.0f, 0.0f); glVertex3fv(vertices[0]);
                glColor3f(1.0f, 0.5f, 0.0f); glVertex3fv(vertices[1]);
                glColor3f(1.0f, 1.0f, 0.0f); glVertex3fv(vertices[2]);
                // 면 2
                glColor3f(0.0f, 1.0f, 0.0f); glVertex3fv(vertices[0]);
                glColor3f(0.0f, 0.0f, 1.0f); glVertex3fv(vertices[2]);
                glColor3f(0.29f, 0.0f, 0.51f); glVertex3fv(vertices[3]);
                // 면 3
                glColor3f(1.0f, 0.0f, 0.0f); glVertex3fv(vertices[0]);
                glColor3f(0.0f, 0.0f, 1.0f); glVertex3fv(vertices[3]);
                glColor3f(1.0f, 0.5f, 0.0f); glVertex3fv(vertices[1]);
                // 면 4
                glColor3f(1.0f, 1.0f, 0.0f); glVertex3fv(vertices[1]);
                glColor3f(0.29f, 0.0f, 0.51f); glVertex3fv(vertices[3]);
                glColor3f(0.0f, 1.0f, 0.0f); glVertex3fv(vertices[2]);
                glEnd();
    
                // 윤곽선 그리기
                glColor3f(1.0f, 1.0f, 1.0f); // 흰색
                glLineWidth(2.0f);
                glBegin(GL_LINES);
                // 면 1의 윤곽선
                glVertex3fv(vertices[0]);
                glVertex3fv(vertices[1]);
                glVertex3fv(vertices[1]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[0]);
                // 면 2의 윤곽선
                glVertex3fv(vertices[0]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[0]);
                // 면 3의 윤곽선
                glVertex3fv(vertices[0]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[1]);
                glVertex3fv(vertices[1]);
                glVertex3fv(vertices[0]);
                // 면 4의 윤곽선
                glVertex3fv(vertices[1]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[2]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[3]);
                glVertex3fv(vertices[1]);
                glEnd();
    
                glPopMatrix();
    
                SwapBuffers(hDC);
    
                theta += 1.0f;
                phi += 1.0f;
                Sleep(1);
            }
        }
    
        /* shutdown OpenGL */
        DisableOpenGL(hWnd, hDC, hRC);
    
        /* destroy the window explicitly */
        DestroyWindow(hWnd);
    
        return msg.wParam;
    }
    
    LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
    {
        switch (message)
        {
        case WM_CREATE:
            return 0;
        case WM_CLOSE:
            PostQuitMessage(0);
            return 0;
    
        case WM_DESTROY:
            return 0;
    
        case WM_SIZE:
            SetupProjection(LOWORD(lParam), HIWORD(lParam));
            return 0;
    
        case WM_KEYDOWN:
            switch (wParam)
            {
            case VK_ESCAPE:
                PostQuitMessage(0);
                return 0;
            }
            return 0;
    
        default:
            return DefWindowProc(hWnd, message, wParam, lParam);
        }
    }
    
    void EnableOpenGL(HWND hWnd, HDC *hDC, HGLRC *hRC)
    {
        PIXELFORMATDESCRIPTOR pfd;
        int iFormat;
    
        /* get the device context (DC) */
        *hDC = GetDC(hWnd);
    
        /* set the pixel format for the DC */
        ZeroMemory(&pfd, sizeof(pfd));
        pfd.nSize = sizeof(pfd);
        pfd.nVersion = 1;
        pfd.dwFlags = PFD_DRAW_TO_WINDOW |
                      PFD_SUPPORT_OPENGL | PFD_DOUBLEBUFFER;
        pfd.iPixelType = PFD_TYPE_RGBA;
        pfd.cColorBits = 24;
        pfd.cDepthBits = 16;
        pfd.iLayerType = PFD_MAIN_PLANE;
        iFormat = ChoosePixelFormat(*hDC, &pfd);
        SetPixelFormat(*hDC, iFormat, &pfd);
    
        /* create and enable the render context (RC) */
        *hRC = wglCreateContext(*hDC);
        wglMakeCurrent(*hDC, *hRC);
    }
    
    void DisableOpenGL(HWND hWnd, HDC hDC, HGLRC hRC)
    {
        wglMakeCurrent(NULL, NULL);
        wglDeleteContext(hRC);
        ReleaseDC(hWnd, hDC);
    }
    
    void SetupProjection(int width, int height)
    {
        glViewport(0, 0, width, height);
        glMatrixMode(GL_PROJECTION);
        glLoadIdentity();
        gluPerspective(45.0, (double)width / (double)height, 1.0, 10.0);
        glMatrixMode(GL_MODELVIEW);
        glLoadIdentity();

##

#### 결과 사진

<img src="https://github.com/GOSUofP5js/workopengl/assets/164286458/21bfcfec-290b-4dff-b603-ee3c9b8a27d3"> <img src="https://github.com/GOSUofP5js/workopengl/assets/164286458/21386164-30c1-4f2c-b4c0-ef1e60f21448">
(실제 실행 화면 에선 3D로 회전합니다.)

---
## 02.  MFC에서 사각형, 타원, 글씨에 다른 3가지 추가하기

#### 코드
##### MFCKIMView.Cpp
        #include "pch.h"
        #include "framework.h"
        #include "MFCKIM.h"
        #include "MFCKIMDoc.h"
        #include "MFCKIMView.h"
        
        #ifdef _DEBUG
        #define new DEBUG_NEW
        #endif
        
        IMPLEMENT_DYNCREATE(CMFCKIMView, CView)
        
        BEGIN_MESSAGE_MAP(CMFCKIMView, CView)
            ON_WM_MOUSEMOVE()
        END_MESSAGE_MAP()
        
        CMFCKIMView::CMFCKIMView() noexcept
        {
        }
        
        BOOL CMFCKIMView::PreCreateWindow(CREATESTRUCT& cs)
        {
            return CView::PreCreateWindow(cs);
        }
        
        void CMFCKIMView::OnDraw(CDC* pDC)
        {
            CMFCKIMDoc* pDoc = GetDocument();
            ASSERT_VALID(pDoc);
            if (!pDoc)
                return;
        
            // 배경 색상 설정
            pDC->FillSolidRect(m_point.x - 200, m_point.y - 200, 400, 400, RGB(255, 255, 255)); // 흰색 배경
        
            // 기존 도형 그리기 (사각형, 타원, 글씨)
            pDC->Rectangle(m_point.x - 150, m_point.y - 150, m_point.x + 150, m_point.y + 150);
            pDC->Ellipse(m_point.x - 150, m_point.y - 150, m_point.x + 150, m_point.y + 150);
            pDC->TextOutW(m_point.x - 30, m_point.y, L"Hello ANU");
        
            // 아이스크림 모양 그리기 (역삼각형 위에 원)
            int icecreamRadius = 100; // 아이스크림 원의 반지름
            int icecreamHeight = 200; // 아이스크림 원의 높이
            POINT icecreamPoints[3]; // 아이스크림 삼각형의 꼭지점 좌표 배열
        
            // 역삼각형 그리기 (콘) - 오른쪽으로 이동
            icecreamPoints[0] = { m_point.x + 95 + 200, m_point.y - 150 }; // 왼쪽 위
            icecreamPoints[1] = { m_point.x - 95 + 200, m_point.y - 150 }; // 오른쪽 위
            icecreamPoints[2] = { m_point.x + 200, m_point.y + 50 };       // 아래
            pDC->SelectObject(GetStockObject(DC_BRUSH));
            pDC->SelectObject(GetStockObject(DC_PEN));
            pDC->SetDCBrushColor(RGB(255, 228, 196)); // 피부색
            pDC->SetDCPenColor(RGB(255, 165, 0));     // 갈색
            pDC->Polygon(icecreamPoints, 3);
        
            // 타원 그리기 (아이스크림) - 오른쪽으로 이동
            pDC->SelectObject(GetStockObject(DC_BRUSH));
            pDC->SelectObject(GetStockObject(DC_PEN));
            pDC->SetDCBrushColor(RGB(255, 255, 0));   // 노란색
            pDC->SetDCPenColor(RGB(255, 165, 0));     // 갈색
            pDC->Ellipse(m_point.x - icecreamRadius + 200, m_point.y - icecreamHeight, m_point.x + icecreamRadius + 200, m_point.y - 50);
        
            // 아이스크림 안에 텍스트 추가
            CFont font;
            font.CreateFontW(20, 0, 0, 0, FW_NORMAL, FALSE, FALSE, FALSE, DEFAULT_CHARSET, OUT_OUTLINE_PRECIS, CLIP_DEFAULT_PRECIS, CLEARTYPE_QUALITY, DEFAULT_PITCH | FF_SWISS, L"Arial");
            pDC->SelectObject(&font);
            pDC->SetTextColor(RGB(0, 0, 255)); // 파란색
            pDC->SetBkMode(TRANSPARENT);
            pDC->TextOutW(m_point.x - 70 + 200, m_point.y - 125, L"Ice Cream~");
        }
        
        
        
        BOOL CMFCKIMView::OnPreparePrinting(CPrintInfo* pInfo)
        {
            return DoPreparePrinting(pInfo);
        }
        
        void CMFCKIMView::OnBeginPrinting(CDC* /*pDC*/, CPrintInfo* /*pInfo*/)
        {
        }
        
        void CMFCKIMView::OnEndPrinting(CDC* /*pDC*/, CPrintInfo* /*pInfo*/)
        {
        }
        
        void CMFCKIMView::OnMouseMove(UINT nFlags, CPoint point)
        {
            m_point = point;
            Invalidate(true);
            CView::OnMouseMove(nFlags, point);
        }
        
        CMFCKIMDoc* CMFCKIMView::GetDocument() const
        {
            ASSERT(m_pDocument->IsKindOf(RUNTIME_CLASS(CMFCKIMDoc)));
            return (CMFCKIMDoc*)m_pDocument;
        }


##### MFCKIMView.h
        #pragma once
        
        class CMFCKIMView : public CView
        {
        protected:
            CMFCKIMView() noexcept;
            DECLARE_DYNCREATE(CMFCKIMView)
        
        public:
            CMFCKIMDoc* GetDocument() const;
        
        public:
            CPoint m_point; // 마우스 좌표
        
        public:
            virtual void OnDraw(CDC* pDC);
            virtual BOOL PreCreateWindow(CREATESTRUCT& cs);
        
        protected:
            virtual BOOL OnPreparePrinting(CPrintInfo* pInfo);
            virtual void OnBeginPrinting(CDC* pDC, CPrintInfo* pInfo);
            virtual void OnEndPrinting(CDC* pDC, CPrintInfo* pInfo);
        
        protected:
            afx_msg void OnMouseMove(UINT nFlags, CPoint point);
            DECLARE_MESSAGE_MAP()
        };


##

#### 결과 사진

<img src="https://github.com/GOSUofP5js/workopengl/assets/164286458/fcd5d3ec-c929-4c87-9d75-5b173b7a8924"> 

---
## 소감문

#### 작성중 ...
