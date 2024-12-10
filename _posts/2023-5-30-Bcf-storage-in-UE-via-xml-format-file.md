---
layout: mypost
title: BCF storage in UE via XML format file
categories: [Research]
date: 2023-5-30
pinned: false
---

## BCF Storage in UE

In our immersive building reviewer,  the comments and screenshots users left can be transfered to a BCF design review software, [BCFier](http://bcfier.com/).

To implement this function, we need to process the comments files in XML format.


###  XML process functions in UE

At present, there is no convenient plug-in in UE for XML-files reading and writing, thus we employed a library named [TinyXML2](https://github.com/leethomason/tinyxml2). 

With this library we can build some XML-process functions. You can find the full code at the end of this post. 


__1. CreateXmlFromFile()__
```
void UMyXml::CreateXmlFromFile(const FString& RootPath, const TMap<FString, FString>& ChildNodes, const FString& path, bool IsRepeatable)
```
<details>
  <summary><b>How it works</b></summary>
  Input a XML file  <code>RootPath</code>, and if needed, create child-nodes.<br> This function will transverse <code>Childnodes</code> and create them in a new XML file, then   save the new file to <code>path</code> .
</details>
<br><br>






__2. InsertNodeByTag()__
```
void UMyXml::InsertNodeByTag(const FString& NodeTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
```
<details>
  <summary><b>How it works</b></summary>
  Input a XML file <code>RootPath</code> and target tag<code>NodeTag</code>.<br>
This function will transverse <code>Childnodes</code> and create them in a new XML file, then find the child-nodes of node with given<code>NodeTag</code>. If these child-nodes already exist and are not repeatable, update their content, otherwise create them.<br>
Then save the  file to <code>RootPath</code>
</details>

<br><br>




__3. InsertNodeByTagSec()__
```
void UMyXml::InsertNodeByTagSec(const FString& SecTag, const FString& FirTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
```
<details>
  <summary><b>How it works</b></summary>
  This function is similar to <code>InsertNodeByTag()</code>, but it processes the child-node with tag<code>SecTag</code> of the parent-node with tag<code>FirTag</code>.
</details>


<br><br>



__4. AddAttributesFir()__
```
void UMyXml::AddAttributesFir(const FString& NodeTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
```
<details>
  <summary><b>How it works</b></summary>
  Input a XML file <code>RootPath</code>and target tag<code>NodeTag</code>.<br>Find the node with tag<code>NodeTag</code>, add the attributes<code>Attributes</code> and save the file. 
</details>


<br><br>









__5. AddAttributesSec()__
```
void UMyXml::AddAttributesSec(const FString& FirTag, const FString& SecTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
```
<details>
  <summary><b>How it works</b></summary>
  This function is similar to <code>AddAttributesFir()</code>, but it processes the child-node with tag<code>SecTag</code> of the parent-node with tag<code>FirTag</code>. 
</details>


<br><br>


###  Transfer data to BCFier

Generally, transfer data to BCFier is to generate a zip-file as bcfzip.  Within bcfzip, data related to review is stored in XML(also a snapshot in PNG).

These XML files are listing in following:

- bcf.version: specifies the version of bcf.
- project.bcfp：stores project id and name.
- markup.bcf:  stores content, object and author of each comment, as well as its viewpoint.
- snapshot.png: the snapshot of the viewpoint.
<br>

Thus, the process will be divided into these steps:

**1. Create XML files named bcf.version and project.bcf;**

**2. Create markup.bcf, insert nodes of Topic, Comment and Viewpoints. Then, create child-nodes and add comment data(Attributes)**

**3.  Make a screenshot as snapshot.png**

**4. Package these fliles as a bcfzip.**

Considering about the article length, the blueprint functions in UE and functions to create zip files will not be discussed here.

<br><br>

###  Full XML process code
```
#include "MyXml.h"
#include "tinyxml2.h"
#include <Engine\World.h>
#include "MyZip.h"
#include "zip.h"
#include "unzip.h"
#include <Runtime/XmlParser/Public/XmlFile.h>
#include <Runtime/Engine/Classes/Components/LineBatchComponent.h>

using namespace tinyxml2;
using namespace std;

void UMyXml::CreateXmlFromFile(const FString& RootPath, const TMap<FString, FString>& ChildNodes, const FString& path, bool IsRepeatable)
{


    FXmlFile* cXmlFile;
    FXmlNode* cRootNode;
    /**
       * 根据传入的根节点名称,拼接处根节点字符串
       *格式为: \n
       *\n 表示换行
       */

    cXmlFile = new FXmlFile(RootPath, EConstructMethod::ConstructFromFile);
    cRootNode = cXmlFile->GetRootNode();
    /*-------------------------------------- - 整合方法----------------------------------------------------------------------------------*/


    //遍历执行创建单个子节点的逻辑,以此完成创建多个子节点的功能
    if (cRootNode != nullptr)
        for (auto childnode : ChildNodes)
        {
            //创建单个子节点
            FString ChildNodeTag = childnode.Key;
            FString InContent = childnode.Value;
            //从根节点查询子节点

            FXmlNode* ChildNode = cRootNode->FindChildNode(ChildNodeTag);

            //判断子节点是否存在
            if (ChildNode != nullptr && !IsRepeatable)
            {
                //如果子节点存在,仅更新文本内容,不创建新的子节点
                ChildNode->SetContent(InContent);
                UE_LOG(LogTemp, Warning, TEXT("%s Already exists,content update finish"), *ChildNodeTag);
                continue;
            }
            //子节点不存在,使用AppendChildNode,传入节点名和节点内容,创建子节点完成
            if (ChildNodeTag != "")
                cRootNode->AppendChildNode(ChildNodeTag, InContent);
        }

    if (cXmlFile != nullptr)
    {
        //将xmlfile对象里的数据,以xml的语法格式存储到本地xml文件中
        cXmlFile->Save(path);
        return;
    }
    UE_LOG(LogTemp, Warning, TEXT("xml file is invalid"));
}

void UMyXml::InsertNodeByTag(const FString& NodeTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
{



    FXmlFile* iXmlFile = nullptr;
    FXmlNode* iRootNode = nullptr;
    FXmlNode* TargetNode = nullptr;
    /**
       * 根据传入的根节点名称,拼接处根节点字符串
       *格式为: \n
       *\n 表示换行
       */
       /**
          * FXmlFile构造函数支持加载本地xml文件和使用一个buffer(根节点字符串)来初始化一个xmlfile对象
          *注意第二个参数,如果是加载本地xml文件,需要设为EConstructMethod::ConstructFromFile
          *如果是通过buffer来初始化,需要设为EConstructMethod::ConstructFromBuffer
          */
    iXmlFile = new FXmlFile(RootPath, EConstructMethod::ConstructFromFile);
    iRootNode = iXmlFile->GetRootNode();
    if (iRootNode != nullptr)
        TargetNode = iRootNode->FindChildNode(NodeTag);

    //遍历执行创建单个子节点的逻辑,以此完成创建多个子节点的功能
    if (TargetNode != nullptr)
        for (auto childnode : ChildNodes)
        {
            //创建单个子节点
            FString ChildNodeTag = childnode.Key;
            FString InContent = childnode.Value;
            //从目标节点查询子节点

            FXmlNode* ChildNode = TargetNode->FindChildNode(ChildNodeTag);

            //判断子节点是否存在
            if (ChildNode != nullptr && !IsRepeatable)
            {
                //如果子节点存在,仅更新文本内容,不创建新的子节点
                ChildNode->SetContent(InContent);
                UE_LOG(LogTemp, Warning, TEXT("%s Already exists,content update finish"), *ChildNodeTag);
                continue;
            }
            //子节点不存在,使用AppendChildNode,传入节点名和节点内容,创建子节点完成
            TargetNode->AppendChildNode(ChildNodeTag, InContent);
        }

    if (iXmlFile != nullptr)
    {
        //将xmlfile对象里的数据,以xml的语法格式存储到本地xml文件中
        iXmlFile->Save(RootPath);
        return;
    }
    UE_LOG(LogTemp, Warning, TEXT("xml file is invalid"));
}

void UMyXml::InsertNodeByTagSec(const FString& SecTag, const FString& FirTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
{

    FXmlFile* iXmlFile = nullptr;
    FXmlNode* iRootNode = nullptr;
    FXmlNode* TargetNode = nullptr;
    /**
       * 根据传入的根节点名称,拼接处根节点字符串
       *格式为: \n
       *\n 表示换行
       */
       /**
          * FXmlFile构造函数支持加载本地xml文件和使用一个buffer(根节点字符串)来初始化一个xmlfile对象
          *注意第二个参数,如果是加载本地xml文件,需要设为EConstructMethod::ConstructFromFile
          *如果是通过buffer来初始化,需要设为EConstructMethod::ConstructFromBuffer
          */
    iXmlFile = new FXmlFile(RootPath, EConstructMethod::ConstructFromFile);
    iRootNode = iXmlFile->GetRootNode();
    if (iRootNode != nullptr)
        TargetNode = iRootNode->FindChildNode(FirTag)->FindChildNode(SecTag);

    //遍历执行创建单个子节点的逻辑,以此完成创建多个子节点的功能
    if (TargetNode != nullptr && !IsRepeatable)
        for (auto childnode : ChildNodes)
        {
            //创建单个子节点
            FString ChildNodeTag = childnode.Key;
            FString InContent = childnode.Value;
            //从目标节点查询子节点

            FXmlNode* ChildNode = TargetNode->FindChildNode(ChildNodeTag);

            //判断子节点是否存在
            if (ChildNode != nullptr)
            {
                //如果子节点存在,仅更新文本内容,不创建新的子节点
                ChildNode->SetContent(InContent);
                UE_LOG(LogTemp, Warning, TEXT("%s Already exists,content update finish"), *ChildNodeTag);
                continue;
            }
            //子节点不存在,使用AppendChildNode,传入节点名和节点内容,创建子节点完成
            TargetNode->AppendChildNode(ChildNodeTag, InContent);
        }

    if (iXmlFile != nullptr)
    {
        //将xmlfile对象里的数据,以xml的语法格式存储到本地xml文件中
        iXmlFile->Save(RootPath);
        return;
    }
    UE_LOG(LogTemp, Warning, TEXT("xml file is invalid"));
}

void UMyXml::AddAttributesFir(const FString& NodeTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
{

    //打开指定路径的xml文件
    XMLDocument* doc = new XMLDocument;
    XMLElement* targetNode = NULL;

    if (XML_SUCCESS != doc->LoadFile(TCHAR_TO_UTF8(*RootPath))) {
        return;
    }


    //根据NodeTag找到指定的一级节点
    XMLElement* root = doc->RootElement();
    if (root != nullptr)
        targetNode = root->FirstChildElement(TCHAR_TO_UTF8(*NodeTag));//初始化


    if (targetNode != nullptr) {

        //遍历执行创建单个属性的逻辑,以此完成创建多个属性的功能
        for (auto attribute : Attributes)
        {
            targetNode->SetAttribute(TCHAR_TO_UTF8(*attribute.Key), TCHAR_TO_UTF8(*attribute.Value));
        }
    }
    //保存文件
    doc->SaveFile(TCHAR_TO_UTF8(*RootPath));
    return;
}

void UMyXml::AddAttributesSec(const FString& FirTag, const FString& SecTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
{

    //打开指定路径的xml文件
    XMLDocument* doc = new XMLDocument;
    XMLElement* targetNode = NULL;
    if (XML_SUCCESS != doc->LoadFile(TCHAR_TO_UTF8(*RootPath))) {
        return;
    }


    //根据FirTag和SecTag找到指定的二级节点
    XMLElement* root = doc->RootElement();
    if (root != nullptr && root->FirstChildElement(TCHAR_TO_UTF8(*FirTag)) != nullptr)
        targetNode = root->FirstChildElement(TCHAR_TO_UTF8(*FirTag))->FirstChildElement(TCHAR_TO_UTF8(*SecTag));//初始化

    if (targetNode != NULL) {

        //遍历执行创建单个属性的逻辑,以此完成创建多个属性的功能
        for (auto attribute : Attributes)
        {
            targetNode->SetAttribute(TCHAR_TO_UTF8(*attribute.Key), TCHAR_TO_UTF8(*attribute.Value));
        }
    }

    //保存文件
    doc->SaveFile(TCHAR_TO_UTF8(*RootPath));
    return;
}

FString UMyXml::getNow()
{
    FDateTime now = FDateTime::Now();
    FString iso = now.ToIso8601();

    return iso;
}
```
