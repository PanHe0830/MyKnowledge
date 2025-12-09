# FEdMode
、、、cpp
## 你需要实现下面的接口 在自己的代码中
class FBuildEdMode : public FEdMode
{
public:
    const static FEditorModeID EM_BuildEdModeId;

public:
    FBuildEdMode();
    virtual ~FBuildEdMode();

    virtual void Enter() override;
    virtual void Exit() override;
	
	virtual bool UsesToolkits() const override { return true; }
	
	const static FText GetDisplayName() { return NSLOCTEXT("BuildEdMode", "DisplayName", "Build Mode"); }
}

const FEditorModeID FBuildEdMode::EM_BuildEdModeId = TEXT("EM_BuildEdMode");

void FBuildEdMode::Enter()
{
	FEdMode::Enter();

    BuildTool = MakeShareable(new FBuildTool());

    if (!Toolkit.IsValid())
    {
        Toolkit = MakeShareable(new FBuildEdModeToolkit);
        StaticCastSharedPtr<FBuildEdModeToolkit>(Toolkit)->Initialize(Owner->GetToolkitHost(), BuildTool);
    }
}

、、、

# FModeToolkit

、、、cpp
## FModeToolkit 需要实现下面的接口用于UI显示
class FBuildEdModeToolkit : public FModeToolkit
{
public:
    void Initialize(const TSharedPtr<IToolkitHost>& InitToolkitHost , const TSharedPtr<class FBuildTool>& BuildTool);
	//void Init(const TSharedPtr<IToolkitHost>& InitToolkitHost);
    //virtual FName GetToolkitFName() const override;
    virtual FText GetBaseToolkitName() const override;
    virtual FEdMode* GetEditorMode() const override;
    virtual TSharedPtr<SWidget> GetInlineContent() const override;
private:
    TSharedPtr<class SWidget> BuildUIWidget;

    TSharedPtr<SCompoundWidget> ToolkitWidget;
}

void FBuildEdModeToolkit::Initialize(const TSharedPtr<IToolkitHost>& InitToolkitHost,const TSharedPtr<FBuildTool>& BuildTool)
{
    ToolkitWidget = SNew(SBuildToolkitWidget).BuildTool(BuildTool); // 使用自定义 SCompoundWidget
    FModeToolkit::Init(InitToolkitHost);
    BuildUIWidget =
        SNew(SBox)
        .WidthOverride(200)
        .HeightOverride(200)
        [
            ToolkitWidget.ToSharedRef()
        ];
}

//FName FBuildEdModeToolkit::GetToolkitFName() const
//{
//    return FName("BuildEdMode");
//}

FText FBuildEdModeToolkit::GetBaseToolkitName() const
{
    return FText::FromString("Build Tool");
}

FEdMode* FBuildEdModeToolkit::GetEditorMode() const
{
    return GLevelEditorModeTools().GetActiveMode(FBuildEdMode::EM_BuildEdModeId);
}

TSharedPtr<SWidget> FBuildEdModeToolkit::GetInlineContent() const
{
    return BuildUIWidget;
}
、、、

# SCompoundWidget
、、、cpp
# UI界面相关
class SBuildToolkitWidget : public SCompoundWidget
{
public:
    SLATE_BEGIN_ARGS(SBuildToolkitWidget) {}
        SLATE_ARGUMENT(TSharedPtr<class FBuildTool>, BuildTool)
    SLATE_END_ARGS()

    void Construct(const FArguments& InArgs);
}

void SBuildToolkitWidget::Construct(const FArguments& InArgs)
{
    BuildTool = InArgs._BuildTool;

    ChildSlot
    [
        SNew(SVerticalBox)
            + SVerticalBox::Slot().AutoHeight().Padding(5)
            [
                SNew(STextBlock).Text(FText::FromString("Build Mode Settings"))
            ]
            + SVerticalBox::Slot().AutoHeight().Padding(5)
            [
                SNew(SObjectPropertyEntryBox)
                    .AllowedClass(UObject::StaticClass()) // 可以改成你想允许的类型
                    .AllowClear(true)
                    .OnObjectChanged(this, &SBuildToolkitWidget::OnResourceSelected)
            ]
    ];
}

、、、