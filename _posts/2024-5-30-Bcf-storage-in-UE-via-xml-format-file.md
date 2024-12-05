---
layout: mypost
title: BCF storage in UE via XML format file
categories: [Research]
date: 2024-5-30
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
  <summary>__How it works__</summary>
  Input a XML file  `RootPath`, and if needed, create child-nodes.<br> This function will transverse `Childnodes` and create them in a new XML file, then   save the new file to `path` .
</details>
<br><br>






__2. InsertNodeByTag()__
```
void UMyXml::InsertNodeByTag(const FString& NodeTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
```
<details>
  <summary>__How it works__</summary>
  Input a XML file `RootPath` and target tag`NodeTag`.<br>
This function will transverse `Childnodes` and create them in a new XML file, then find the child-nodes of node with given`NodeTag`. If these child-nodes already exist and are not repeatable, update their content, otherwise create them.<br>
Then save the  file to `RootPath`
</details>

<br><br>




__3. InsertNodeByTagSec()__
```
void UMyXml::InsertNodeByTagSec(const FString& SecTag, const FString& FirTag, const TMap<FString, FString>& ChildNodes, const FString& RootPath, bool IsRepeatable)
```
<details>
  <summary>__How it works__</summary>
  This function is similar to InsertNodeByTag(), but it processes the child-node with tag`SecTag` of the parent-node with tag`FirTag`.
</details>


<br><br>



__4. AddAttributesFir()__
```
void UMyXml::AddAttributesFir(const FString& NodeTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
```
<details>
  <summary>__How it works__</summary>
  Input a XML file `RootPath` and target tag`NodeTag`.<br>Find the node with tag`NodeTag`, add the attributes`Attributes` and save the file. 
</details>


<br><br>









__5. AddAttributesSec()__
```
void UMyXml::AddAttributesSec(const FString& FirTag, const FString& SecTag, const TMap<FString, FString>& Attributes, const FString& RootPath)
```
<details>
  <summary>__How it works__</summary>
  This function is similar to AddAttributesFir(), but it processes the child-node with tag`SecTag` of the parent-node with tag`FirTag`. 
</details>


<br><br>


# TBC
