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
  This function is similar to InsertNodeByTag(), but it processes the child-node with tag<code>SecTag</code> of the parent-node with tag<code>FirTag</code>.
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


# TBC
