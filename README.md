# -
//这个学生宿舍管理系统是有数据库和visual c++软件做的
//数据表格很简单
//关键在于页面的转换与输入密码之类的事情，还有增 删 改查的事情。
// Operation.cpp : 实现文件
//

#include "stdafx.h"
#include "学生宿舍管理系统试运行.h"
#include "Operation.h"
#include "afxdialogex.h"
#include "student0.h"
#include "addDlg.h"
#include "account.h"
#include "changeInfo.h"



// Operation 对话框

IMPLEMENT_DYNAMIC(Operation, CDialogEx)

Operation::Operation(CWnd* pParent /*=NULL*/)
	: CDialogEx(Operation::IDD, pParent)
{

}

Operation::~Operation()
{
}

void Operation::DoDataExchange(CDataExchange* pDX)
{
	CDialogEx::DoDataExchange(pDX);
	DDX_Control(pDX, IDC_LIST1, m_listCtrl);
}


BEGIN_MESSAGE_MAP(Operation, CDialogEx)
	ON_BN_CLICKED(IDC_BUTTON1, &Operation::OnBnClickedButton1)
	ON_BN_CLICKED(IDC_BUTTON_DEL, &Operation::OnBnClickedButtonDel)
	ON_BN_CLICKED(IDC_BUTTON_CHANGE, &Operation::OnBnClickedButtonChange)
END_MESSAGE_MAP()


// Operation 消息处理程序


BOOL Operation::OnInitDialog()
{
	
// TODO:  在此添加额外的初始化
	CDialog::OnInitDialog ();
	CString strHeader[]={"学号","学生姓名","性别","入学年份","专业","宿舍类型","楼号","房间号","宿舍电话","备注"};
	int nLong[]={100,100,100,100,100,120,100,100,100,100};
	for(int nCol=0;nCol<sizeof(strHeader)/sizeof(CString);nCol++)
		m_listCtrl.InsertColumn(nCol,strHeader[nCol],LVCFMT_LEFT,nLong[nCol]);

	//创建列表项
	Cstudent0 sSet;
	sSet.Open();
	int nItem=0;
	CString str;
	while(!sSet.IsEOF()){
		for(UINT i=0;i<9;i++){
		sSet.GetFieldValue(i,str);
		if(i==0)
			m_listCtrl.InsertItem(nItem,str);
		else
			m_listCtrl.SetItemText(nItem,i,str);}
	/*
	m_listCtrl.SetItemText(nItem,6,sSet.m_Studentname );
	*/
		sSet.MoveNext ();
	}
    sSet.Close();

	return TRUE;  // return TRUE unless you set the focus to a control
}


void Operation::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	Cstudent0 sSet;
	addDlg dlg;
	if(dlg.DoModal ()==IDOK){
		sSet.Open();
		sSet.AddNew ();
		if (dlg.m_bMale==1)
			sSet.m_Sex="男";
		else
			sSet.m_Sex="女";
		sSet.m_Studentname =dlg.m_strStudentname ;
		sSet.m_Id =dlg.m_strId ;
		//sSet.m_Startyear =dlg.m_time;
		sSet.m_Major =dlg.m_strMajor ;
		sSet .m_Roomtype =dlg.m_strRoomtype ;
		//sSet .m_Buildingnum =dlg.m_nBuildingnum ;
		//sSet .m_Bednum =dlg.m_nBednum ;
		sSet .m_Housetel =dlg.m_strHouseTel ;
		sSet .m_Remarks =dlg.m_strRemarks ;
		sSet .Update ();
		sSet .Requery ();


	}

}


void Operation::OnBnClickedButtonDel()
{
	// TODO: 在此添加控件通知处理程序代码
	POSITION pos;
	pos=m_listCtrl.GetFirstSelectedItemPosition();
	if(pos==NULL)
	{MessageBox("还没有选中列表项");
	return;}
	int nItem=m_listCtrl.GetNextSelectedItem (pos);
	CString m_del_id1=m_listCtrl.GetItemText(nItem,0);
	//CString m_del_id2=m_listCtrl.GetItemText(nItem,6);
	//CString m_del_id3=m_listCtrl.GetItemText(nItem,8);
	if(IDYES==MessageBox("确定要删除吗？","警告",MB_ICONWARNING|MB_YESNO))
	{
		m_listCtrl.DeleteItem(nItem);
	}
	Cstudent0 sSet;
	sSet.m_strFilter.Format("ID='%s'",m_del_id1);
	//MessageBox(m_del_id1);
	sSet.Open();
	CRecordsetStatus status1;
	sSet.GetStatus(status1);
	sSet.Delete();
	if(status1.m_lCurrentRecord ==0)
		sSet.MoveNext ();
	else
		sSet.MoveFirst ();

	UpdateData(FALSE);      //将控件的记录传到变量中
}


void Operation::OnBnClickedButtonChange()
{
	// TODO: 在此添加控件通知处理程序代码
	POSITION pos;
	pos=m_listCtrl.GetFirstSelectedItemPosition();
	if(pos==NULL){
		MessageBox("还没有选中列表项");
		return;

	}
	int nItem=m_listCtrl.GetNextSelectedItem (pos);
	changeInfo dlg;
	dlg.m_strId=m_listCtrl.GetItemText(nItem,0);
	dlg.m_strStudentname=m_listCtrl.GetItemText(nItem,1);
		//dlg.m_=m_listCtrl.GetItemText(nItem,2);
			//dlg.m_strStudentname=m_listCtrl.GetItemText(nItem,3);
			dlg.m_strMajor=m_listCtrl.GetItemText(nItem,4);
		dlg.m_strRoomtype=m_listCtrl.GetItemText(nItem,5);
		dlg.m_strBuildingnum=m_listCtrl.GetItemText(nItem,6);
		dlg.m_strBednum=m_listCtrl.GetItemText(nItem,7);
			dlg.m_strHouseTel=m_listCtrl.GetItemText(nItem,8);
			dlg.m_strRemarks=m_listCtrl.GetItemText(nItem,9);
	Caccount pSet;
	Cstudent0 sSet;
	if(dlg.DoModal ()==IDOK)
	{
		sSet.m_strFilter.Format("ID='%s'",m_listCtrl.GetItemText(nItem,0));
		sSet.Open();
		sSet.Edit();
		sSet.m_Studentname =dlg.m_strStudentname ;
		if(dlg.m_bMale ==1)
			sSet.m_Sex ="男";
		else
			sSet.m_Sex ="女";
		sSet.Update();
		sSet.Requery ();
	}
}
BEGIN_MESSAGE_MAP(changeInfo, CDialogEx)
	ON_BN_CLICKED(IDC_MALE, &changeInfo::OnBnClickedMale)
	ON_BN_CLICKED(IDC_FEMALE, &changeInfo::OnBnClickedFemale)
	ON_BN_CLICKED(IDOK, &changeInfo::OnBnClickedOk)
END_MESSAGE_MAP()


// changeInfo 消息处理程序


void changeInfo::OnBnClickedMale()
{
	// TODO: 在此添加控件通知处理程序代码
	m_bMale=TRUE;
}


void changeInfo::OnBnClickedFemale()
{
	// TODO: 在此添加控件通知处理程序代码
	m_bMale=FALSE;
}


BOOL changeInfo::OnInitDialog()
{
	CDialogEx::OnInitDialog();

if(!m_bMale)
		CheckRadioButton(IDC_MALE,IDC_FEMALE,IDC_FEMALE);
	else
		CheckRadioButton(IDC_MALE,IDC_FEMALE,IDC_MALE);
	UpdateData(FALSE);      //将控件的值传入变量
	return TRUE;
}


void changeInfo::OnBnClickedOk()
{
	// TODO: 在此添加控件通知处理程序代码
	CDialogEx::OnOK();
}

// 学生宿舍管理系统试运行Dlg.cpp : 实现文件
//

#include "stdafx.h"
#include "学生宿舍管理系统试运行.h"
#include "学生宿舍管理系统试运行Dlg.h"
#include "afxdialogex.h"
#include "account.h"
#include "Register.h"
#include "changePassword.h"
#include "Operation.h"

#ifdef _DEBUG
#define new DEBUG_NEW
#endif


// 用于应用程序“关于”菜单项的 CAboutDlg 对话框

class CAboutDlg : public CDialogEx
{
public:
	CAboutDlg();

// 对话框数据
	enum { IDD = IDD_ABOUTBOX };

	protected:
	virtual void DoDataExchange(CDataExchange* pDX);    // DDX/DDV 支持

// 实现
protected:
	DECLARE_MESSAGE_MAP()
};

CAboutDlg::CAboutDlg() : CDialogEx(CAboutDlg::IDD)
{
}

void CAboutDlg::DoDataExchange(CDataExchange* pDX)
{
	CDialogEx::DoDataExchange(pDX);
}

BEGIN_MESSAGE_MAP(CAboutDlg, CDialogEx)
END_MESSAGE_MAP()


// C学生宿舍管理系统试运行Dlg 对话框



C学生宿舍管理系统试运行Dlg::C学生宿舍管理系统试运行Dlg(CWnd* pParent /*=NULL*/)
	: CDialogEx(C学生宿舍管理系统试运行Dlg::IDD, pParent)
	, m_strAccount(_T(""))
	, m_strPassword(_T(""))
{
	m_hIcon = AfxGetApp()->LoadIcon(IDR_MAINFRAME);
}

void C学生宿舍管理系统试运行Dlg::DoDataExchange(CDataExchange* pDX)
{
	CDialogEx::DoDataExchange(pDX);
	DDX_Control(pDX, IDC_BUTTON_LOGIN, m_bnLogIn);
	DDX_Control(pDX, IDC_BUTTON_LOGOUT, m_bnLogOut);
	DDX_Text(pDX, IDC_EDIT_ACCOUNT, m_strAccount);
	DDX_Text(pDX, IDC_EDIT_PASSWORD, m_strPassword);
}

BEGIN_MESSAGE_MAP(C学生宿舍管理系统试运行Dlg, CDialogEx)
	ON_WM_SYSCOMMAND()
	ON_WM_PAINT()
	ON_WM_QUERYDRAGICON()
	ON_BN_CLICKED(IDC_BUTTON_LOGIN, &C学生宿舍管理系统试运行Dlg::OnBnClickedButtonLogin)
	ON_BN_CLICKED(IDC_BUTTON_LOGOUT, &C学生宿舍管理系统试运行Dlg::OnBnClickedButtonLogout)
	ON_BN_CLICKED(IDC_BUTTON_CHANGEPASSWORD, &C学生宿舍管理系统试运行Dlg::OnBnClickedButtonChangepassword)
END_MESSAGE_MAP()


// C学生宿舍管理系统试运行Dlg 消息处理程序

BOOL C学生宿舍管理系统试运行Dlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// 将“关于...”菜单项添加到系统菜单中。

	// IDM_ABOUTBOX 必须在系统命令范围内。
	ASSERT((IDM_ABOUTBOX & 0xFFF0) == IDM_ABOUTBOX);
	ASSERT(IDM_ABOUTBOX < 0xF000);

	CMenu* pSysMenu = GetSystemMenu(FALSE);
	if (pSysMenu != NULL)
	{
		BOOL bNameValid;
		CString strAboutMenu;
		bNameValid = strAboutMenu.LoadString(IDS_ABOUTBOX);
		ASSERT(bNameValid);
		if (!strAboutMenu.IsEmpty())
		{
			pSysMenu->AppendMenu(MF_SEPARATOR);
			pSysMenu->AppendMenu(MF_STRING, IDM_ABOUTBOX, strAboutMenu);
		}
	}

	// 设置此对话框的图标。当应用程序主窗口不是对话框时，框架将自动
	//  执行此操作
	SetIcon(m_hIcon, TRUE);			// 设置大图标
	SetIcon(m_hIcon, FALSE);		// 设置小图标

	// TODO: 在此添加额外的初始化代码

	return TRUE;  // 除非将焦点设置到控件，否则返回 TRUE
}

void C学生宿舍管理系统试运行Dlg::OnSysCommand(UINT nID, LPARAM lParam)
{
	if ((nID & 0xFFF0) == IDM_ABOUTBOX)
	{
		CAboutDlg dlgAbout;
		dlgAbout.DoModal();
	}
	else
	{
		CDialogEx::OnSysCommand(nID, lParam);
	}
}

// 如果向对话框添加最小化按钮，则需要下面的代码
//  来绘制该图标。对于使用文档/视图模型的 MFC 应用程序，
//  这将由框架自动完成。

void C学生宿舍管理系统试运行Dlg::OnPaint()
{
	if (IsIconic())
	{
		CPaintDC dc(this); // 用于绘制的设备上下文

		SendMessage(WM_ICONERASEBKGND, reinterpret_cast<WPARAM>(dc.GetSafeHdc()), 0);

		// 使图标在工作区矩形中居中
		int cxIcon = GetSystemMetrics(SM_CXICON);
		int cyIcon = GetSystemMetrics(SM_CYICON);
		CRect rect;
		GetClientRect(&rect);
		int x = (rect.Width() - cxIcon + 1) / 2;
		int y = (rect.Height() - cyIcon + 1) / 2;

		// 绘制图标
		dc.DrawIcon(x, y, m_hIcon);
	}
	else
	{
		CDialogEx::OnPaint();
	}
}

//当用户拖动最小化窗口时系统调用此函数取得光标
//显示。
HCURSOR C学生宿舍管理系统试运行Dlg::OnQueryDragIcon()
{
	return static_cast<HCURSOR>(m_hIcon);
}



void C学生宿舍管理系统试运行Dlg::OnBnClickedButtonLogin()
{
	// TODO: 在此添加控件通知处理程序代码
	Caccount pSet;
	UpdateData();
	m_strAccount.TrimLeft();
	m_strPassword.TrimLeft();
	if(m_strAccount.IsEmpty()||m_strPassword.IsEmpty())
	{
		AfxMessageBox(TEXT("账号，密码不能为空！"));
		return;
	}
	if(pSet.IsOpen())
		pSet.Close();
	pSet.m_strFilter .Format ("account='%s'",m_strAccount);
	pSet.Open();
	if(!pSet.IsEOF())        //如果记录集不为空
	{
		pSet.Close();
		pSet.m_strFilter .Format ("account='%s'and password='%s'",m_strAccount,m_strPassword);
		pSet.Open();
		if(!pSet.IsEOF())
		{
			MessageBox("登录成功");
			pSet.Close();
			OnCancel();
			Operation dlg;
			//C学生宿舍Dlg dlg;
			dlg.DoModal ();

		}
		else
			MessageBox("密码错误");
	}
}


void C学生宿舍管理系统试运行Dlg::OnBnClickedButtonLogout()
{
	// TODO: 在此添加控件通知处理程序代码
	CRegister dlg;
	dlg.DoModal ();
}


void C学生宿舍管理系统试运行Dlg::OnBnClickedButtonChangepassword()
{
	// TODO: 在此添加控件通知处理程序代码
	Caccount cSet;
	changePassword dlg;
	if(dlg.DoModal ()==IDOK){
		cSet.Open();
		cSet.Edit();
		cSet.m_password =dlg.m_strNewPassword ;     //确定新密码即代表新密码值
		cSet.Update();
		cSet.Requery ();

	}
}
// addDlg.cpp : 实现文件
//

#include "stdafx.h"
#include "学生宿舍管理系统试运行.h"
#include "addDlg.h"
#include "afxdialogex.h"
#include "student0.h"


// addDlg 对话框

IMPLEMENT_DYNAMIC(addDlg, CDialogEx)

addDlg::addDlg(CWnd* pParent /*=NULL*/)
	: CDialogEx(addDlg::IDD, pParent)
	, m_bMale(false)
	, m_nBednum()
	, m_nBuildingnum()
	, m_strHouseTel(_T(""))
	, m_strId(_T(""))
	, m_strMajor(_T(""))
	, m_strRemarks(_T(""))
	, m_strRoomtype(_T(""))
	, m_strStudentname(_T(""))
	/* , m_strBuildingnum(_T(""))
	, m_nBednum(_T(""))
	, m_nBuildingnum(_T("")*/
	
{

}

addDlg::~addDlg()
{
}

void addDlg::DoDataExchange(CDataExchange* pDX)
{
	CDialogEx::DoDataExchange(pDX);
	//  DDX_Text(pDX, IDC_EDIT_BED, m_nBednum);
	//  DDX_Text(pDX, IDC_EDIT_BUILDINGNUM, m_nBuildingnum);
	DDX_Text(pDX, IDC_EDIT_HOUSETEL, m_strHouseTel);
	DDX_Text(pDX, IDC_EDIT_ID, m_strId);
	DDX_Text(pDX, IDC_EDIT_MAJOR, m_strMajor);
	DDX_Text(pDX, IDC_EDIT_REMARKS, m_strRemarks);
	DDX_Text(pDX, IDC_EDIT_ROOMTYPE, m_strRoomtype);
	DDX_Text(pDX, IDC_EDIT_STUDENTNAME, m_strStudentname);
	DDX_Control(pDX, IDC_DATETIMEPICKER1, m_time);
	//  DDX_Text(pDX, IDC_EDIT_BED, m_strBuildingnum);
	DDX_Text(pDX, IDC_EDIT_BED, m_nBednum);
	DDX_Text(pDX, IDC_EDIT_BUILDINGNUM, m_nBuildingnum);
}


BEGIN_MESSAGE_MAP(addDlg, CDialogEx)
	ON_BN_CLICKED(IDC_MALE, &addDlg::OnBnClickedRadio1)
	ON_BN_CLICKED(IDC_FEMALE, &addDlg::OnBnClickedRadio2)
	ON_BN_CLICKED(IDOK, &addDlg::OnBnClickedOk)
END_MESSAGE_MAP()


// addDlg 消息处理程序


void addDlg::OnBnClickedRadio1()
{
	// TODO: 在此添加控件通知处理程序代码
	m_bMale=TRUE;
}


void addDlg::OnBnClickedRadio2()
{
	// TODO: 在此添加控件通知处理程序代码
	m_bMale=FALSE;
}


BOOL addDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();
/*
#5312

	return TRUE;  // return TRUE unless you set the focus to a control
#5313

#5312

	return TRUE;  // return TRUE unless you set the focus to a 
	
#5313
	*/
	// TODO:  在此添加额外的初始化
	if(!m_bMale)
		CheckRadioButton(IDC_MALE,IDC_FEMALE,IDC_FEMALE);
	else
		CheckRadioButton(IDC_MALE,IDC_FEMALE,IDC_MALE);
	UpdateData(FALSE);
	return TRUE;  // return TRUE unless you set the focus to a control
	/*
#5313
	return TRUE;  // return TRUE unless you set the focus to a control
#5313
	// 异常: OCX 属性页应返回 FALSE
	*/
}


void addDlg::OnBnClickedOk()
{
	// TODO: 在此添加控件通知处理程序代码
	Cstudent0 sSet;
	UpdateData();    //将控件中的信息传入到变量中去
	m_strStudentname.TrimLeft ();
	m_strId.TrimLeft ();
	m_strMajor .TrimLeft ();
	m_strRoomtype .TrimLeft ();
	m_nBuildingnum .TrimLeft();
	m_nBednum.TrimLeft();
	m_strHouseTel .TrimLeft ();
	m_strRemarks .TrimLeft ();

	if(m_strStudentname .IsEmpty ()||m_strId .IsEmpty ()||m_strMajor.IsEmpty ()||
		m_strRoomtype .IsEmpty ()||m_nBuildingnum .IsEmpty ()||m_nBednum .IsEmpty ()||
		m_strHouseTel .IsEmpty ()||m_strRemarks .IsEmpty ())
		MessageBox("不能有空项！");
	else
	{
		sSet.m_strFilter .Format("ID='%s'",m_strId);
		sSet.Open();//打开记录集
		if(!sSet.IsEOF())
		{
			//already recorded
			MessageBox("该ID信息已经添加。");
			sSet.Close();
		}
		else
		{
			MessageBox("添加成功！");
			CDialog::OnOK();
		}
	}


		
	CDialogEx::OnOK();
}
