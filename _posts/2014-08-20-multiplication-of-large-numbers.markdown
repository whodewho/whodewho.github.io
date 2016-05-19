---
layout: post
title:  "大数乘法之python vs c++"
category: programming
tags: [c++, python, algorithm]
---

《黑客与画家》提到，用lisp、python、ruby的团队会显得比java、 c++更可怕，因为他们在更短的时间里可以做更多的事。

##python实现
{% highlight python linenos %}
def mul(s1, s2)
    return Decimal(s1)*Decimal(s2)

mul("123456789", "99999999")
mul("12345.6789", "99999999")
mul("12345.6789", "99999.999")
print("-----------------------")
mul("-12345.6789", "99999999")
mul("-12345.6789", "-9.9999999")
mul("-12345.6789", "-9.99999990000")
print("-----------------------")
mul("-12345.6789", "0.99999999")
mul("-12.3456789", "+0.000000099999999")
{% endhighlight %}

##c++实现

{% highlight c++ linenos %}
string mul(string s1, string s2)
{
	//handle sign bit
	int sign=1;
	if(s1.empty()||s2.empty())return "";
	if(s1[0]=='+'||s1[0]=='-')
	{
		if(s1[0]=='-')sign=sign*(-1);
		s1.erase(s1.begin());
	}
	if(s2[0]=='+'||s2[0]=='-')
	{
		if(s2[0]=='-')sign=sign*(-1);
		s2.erase(s2.begin());
	}

	//handle float number with '.'
	int point=0;
	std::size_t s1p=s1.find(".");
	if(s1p!=std::string::npos)
	{
		point+=s1.size()-1-s1p;
		s1.erase(s1.begin()+s1p);
	}
	std::size_t s2p=s2.find(".");
	if(s2p!=std::string::npos)
	{
		point+=s2.size()-1-s2p;
		s2.erase(s2.begin()+s2p);
	}

	//multiply two big int number, 
	//number might starts or ends with '0', that's ok
	int m=s1.size();
	int n=s2.size();
	vector<int> result(m+n, 0);
	reverse(s1.begin(), s1.end());
	reverse(s2.begin(), s2.end());
	int carry=0;
	for(int i=0;i<m;i++)
	{
		carry=0;
		for(int j=0;j<n;j++)
		{
			int tmpM=(s1[i]-'0')*(s2[j]-'0');
			int tmpR=(tmpM+carry+result[i+j])%10;
			carry=(tmpM+carry+result[i+j])/10;
			result[i+j]=tmpR;	
		}
		if(carry)result[i+n]=carry;
	}
	if(carry)result[m+n-1]=carry;
	reverse(result.begin(), result.end());
	string tmp;
	for(int i=0;i<result.size();i++)
	{
		tmp.push_back(result[i]+'0');
	}

	//handle '.'
	if(point)
	{
		//insert the '.' into the string
		tmp.insert(tmp.end()-point, '.');
		//trim the '0' before '0.'
		while(tmp[0]=='0')tmp.erase(tmp.begin());
		if(tmp[0]=='.')tmp.insert(tmp.begin(), '0');
	}
	else
	{
		while(tmp[0]=='0')tmp.erase(tmp.begin());
	}
	//insert sign bit
	if(sign<0)tmp.insert(tmp.begin(), '-');
	return tmp;
}

int main()
{
	cout<<mul("123456789", "99999999")<<endl;
	cout<<mul("12345.6789", "99999999")<<endl;
	cout<<mul("12345.6789", "99999.999")<<endl;
	cout<<"-----------------------------"<<endl;
	cout<<mul("-12345.6789", "99999999")<<endl;
	cout<<mul("-12345.6789", "-9.9999999")<<endl;
	cout<<mul("-12345.6789", "-9.99999990000")<<endl;
	cout<<"-----------------------------"<<endl;
	cout<<mul("-12345.6789", "0.99999999")<<endl;
	cout<<mul("-12.3456789", "+0.000000099999999")<<endl;

	return 0;
}
{% endhighlight %}



