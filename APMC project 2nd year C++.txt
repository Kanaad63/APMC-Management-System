//APMC (Agricultural Produce Market Committee) Management System
#include<iostream>
#include <fstream>
using namespace std;


class farmer
{

protected : char name[25];
            char id[10];
            char prod[10];
            float quantity;

public :
        void getf()
       {
        cout<<"\nEnter the name\n";
        cin>>name;
        cout<<"\nEnter the id start with F\n";
        cin>>id;
        cout<<"\nEnter the product name to be sold\n";
        cin>>prod;
        cout<<"\nEnter the quantity of "<<prod<<" to be sold in quintals(1 quintal =100 kg)\n";
        cin>>quantity;
       }

};


class dealer
{

protected : char name1[25];
            char id1[10];

public :
        void getd()
       {
        cout<<"\nEnter the name\n";
        cin>>name1;
        cout<<"\nEnter the id start with D\n";
        cin>>id1;
       }

};


class apmc: public farmer,public dealer
{

    float msp,price,amount;
    float maxi=0.00;
    int next;
    char date[10];

public :
        void getdataf()
        {
        getf();
        }

        void getdatad()
        {
        getd();
        }

        void putdata(apmc deal[],int n);
        void auction(apmc deal[],int n);
        void rec(apmc deal[],int n);

};


void apmc :: auction(apmc deal[],int n)
{

 cout<<"\n\n\n\nAuction starts :\n";
 cout<<"\nEnter the date in DD/MM/YYYY format\n";
 cin>>date;
 cout<<"\nEnter the MSP for the product '"<<prod<<"' fixed by government per kg\n";
 cin>>msp;
 cout<<"\nEnter the bidding price for the product '"<<prod<<"' one by one\n";
 cout<<"Bidding price should be always greater than or equal to MSP fixed and greater than any another dealer\n";

 int round=1;
 for(;;)
 {

    cout<<"\n\n\n\nRound "<<round<<" :\n";
    for(int i=0;i<n;i++)
   {
    int choice;
    cout<<"\n\nIf '"<<deal[i].name1<<"' wants to bid for round-"<<round<<" enter 1 else enter any\n";
    cin>>choice;

    if(choice==1)
     {
        cout<<"\ndealer '"<<deal[i].name1<<"' bids for rupees\n";
        cin>>deal[i].price;
        if(deal[i].price<msp)
        {
            cout<<"\nInvalid\n";
        }
        if(deal[i].price>maxi)
            maxi=deal[i].price;
      }
    }

   ++round;
   cout<<"\nIf any dealer wants to go for round-"<<round<<" enter 1 else enter any\n";
   cin>>next;

   if(next!=1)
   {
   cout<<"\nAuction completed\n";
   return;
   }

 }

}


void apmc :: putdata(apmc deal[],int n)
{

    for(int i=0;i<n;i++)
   {
      if(maxi==deal[i].price)
     {
        if(maxi<msp)
        {
            cout<<"Invalid - Deal is not performed\n";
            return;
        }
        else
        {
            cout<<"\n\n\n\n\t\tThe deal is performed between the farmer '"<<name<<"' and the dealer '"<<deal[i].name1<<"'\n";
            cout<<"\t\tDealer '"<<deal[i].name1<<"' buys the product '"<<prod<<"' for rupees '"<<deal[i].price<<"' per kg "<<"(MSP was "<<msp<<" rupees)\n";
            amount=(quantity*100*deal[i].price);
            cout<<"\t\tSo he has to pay rupees '"<<amount<<"' for '"<<quantity<<"' quintals of product '"<<prod<<"'\n";
           return;
        }
     }
   }

}


void apmc :: rec(apmc deal[],int n)
{

    int choice;
    cout<<"\n\n\n\nIf the deal is performed then to update the File enter 1 else enter any\n";
    cin>>choice;
    if(choice!=1)
    {
      cout<<"File is not updated\n";
      return;
    }


    ofstream ap("apmc.txt",ios::out | ios::app);
    if(!ap)
    {
         cout<<"Cannot open apmc file.\n";
         return;
    }

    ap<<"--------------------------------------\n";
    ap<<"\n"<<date<<"\n";
    ap<<"Details of auction performed\n\n";

    ap<<"Farmer name \n"<<name<<" (ID "<<id<<")\n";
    ap<<"Product name \n"<<prod<<"\n";
    ap<<"Quantity of the product \n"<<quantity<<" quintals\n\n";

    for(int i=0;i<n;i++)
    ap<<"Dealer "<<(i+1)<<" name \n"<<deal[i].name1<<" (ID "<<deal[i].id1<<")\n";

    for(int i=0;i<n;i++)
   {
      if(maxi==deal[i].price)
     {
         ap<<"\nMSP "<<msp<<" rupees\n";
         ap<<"The deal is performed between farmer '"<<name<<"' and dealer '"<<deal[i].name1<<"'\n";
         ap<<"Dealer '"<<deal[i].name1<<"' buys the product '"<<prod<<"' for rupees '"<<deal[i].price<<"' per kg\n";
         ap<<"So he paid rupees '"<<amount<<"' for '"<<quantity<<"' quintals of product '"<<prod<<"'\n";
         ap<<"\n--------------------------------------\n";
     }
   }
    cout<<"File is updated successfully\n\n";
    ap.close();

}


int main()
{

 apmc farm;
 int i,n,ext;

 for(;;)
 {

    cout<<"\n\n-------------------------------------------\n";
    cout<<"\nA New Deal Begins\n";
    cout<<"\n-------------------------------------------\n";


    cout<<"\nEnter the farmer's details and his product details\n\n";
    farm.getdataf();


    cout<<"\n\n\nEnter the no of dealers in the auction\n";
    cin>>n;
    apmc deal[n];
    cout<<"Enter the dealers' details\n\n";
    for(i=0;i<n;i++)
    {
        cout<<"\nEnter the details of the dealer "<<(i+1)<<"\n";
        deal[i].getdatad();
    }


    farm.auction(deal,n);
    farm.putdata(deal,n);
    farm.rec(deal,n);

    int op;
    cout<<"\n\nTo show records enter 1 else enter any\n";
    cin>>op;
    if(op==1)
    {

    ifstream in("apmc.txt", ios::in);
    if(!in)
    {
         cout<<"Cannot open apmc file.\n";
         return 0;
    }

    char ch;
    in.seekg(0, ios::beg);
    while(!in.eof())
    {
      in.get(ch);
      if(!in.eof())
      cout << ch;
    }

    }


    cout<<"\n\n\n\nIf you want to go for a new auction enter 1 else to exit enter any\n";
    cin>>ext;
    if(ext!=1)
    {
    printf("\nExiting\n");
    return 0;
    }

 }

}