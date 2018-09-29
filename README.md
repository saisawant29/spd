# spd

PROJECT FILES

basicapp/db_settings.py

db_host="172.25.4.71"
db_user="root"
db_pwd="root123"
db_database="Details"



basicapp/views.py

from django.shortcuts import render
from django.core.exceptions import ValidationError
from datetime import datetime as dt
from django.http import HttpResponseRedirect
from basicapp.db_settings import db_host,db_user,db_pwd,db_database
import pymysql
import re
import numpy as np
import matplotlib.pyplot as plt
import decimal
from django.contrib import messages

# Create your views here.

def charts(request):
    return render(request,"charts.html")

def login(request):
    return render(request,"login.html")

def EntryDetails(request):
    print("INSIDE ENTRY DETAILSSSSSSSS")
    if request.method=='POST':
        sai=request.POST
        print("SAIIIIIIIIIIIIIIIIIIIIIIIII")
        print(sai)
        btnlogin=request.POST.get('btnlogin')
        logoutbtn=request.POST.get('logoutbtn')
        btnshowchart=request.POST.get('btnshowchart')
        backbtn=request.POST.get('backbtn')
        findbtn=request.POST.get('findbtn')
        submitbtn=request.POST.get('submitbtn')
        btndwnldrep=request.POST.get('btndwnldrep')
        txtfromdate=request.POST.get('txtfromdate')
        txttodate=request.POST.get('txttodate')
        txtfiltermob=request.POST.get('txtfiltermob')
        txtCustMob=request.POST.get('txtCustMob')
        txtMobile=request.POST.get('txtMobile')
        txtCustName=request.POST.get('txtCustName')
        ddlNewOldCust=request.POST.get('ddlNewOldCust')
        ddlGender=request.POST.get('ddlGender')
        ddlAgeGroup=request.POST.get('ddlAgeGroup')
        ddlTBZ=request.POST.get('ddlTBZ')
        if btnlogin == "Login":
            global form_no,today_dt,userid_val,branch_val
            txtUsername = request.POST.get('txtUsername')
            txtPwd = request.POST.get('txtPwd')
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            print("BEFORE QUERY!!!!!!!!!!")
            rows_data = curr.execute("SELECT * from Branch where UserId = '{}' and py_passwd ='{}'".format(txtUsername,txtPwd))
            login_data = curr.fetchall()
            print("LOGIN DATAAAAAAAAAAAAAAA!!!!!!!!!!")
            print(login_data)
            if len(login_data) == 0:
                print("INSIDEEEEEEEEEEE LEN = 0")
                messages.info(request, 'Incorrect credentials!')
                return HttpResponseRedirect("/")
            else:
                userid_val=login_data[0][0]
                branch_val=login_data[0][2]
                print(userid_val)
                print(branch_val)
                rows = curr.execute("SELECT FormNo,date_format(curdate(), '%d-%m-%Y') FROM EntryDetails ORDER BY FormNo DESC LIMIT 1;")
                db_output_list = curr.fetchall()
                form_no = db_output_list[0][0] + 1
                today_dt = db_output_list[0][1]
                print("INSIDE LOGINNNNNNNNNNNNNNN2222222222222222222222222222")
                print(form_no)
                print(today_dt)
                return render(request,"demo2.html",{'form_no':form_no,'today_dt':today_dt,'branch_val':branch_val})
            # return render(request,"login.html")
        elif logoutbtn == "Log out":
            return HttpResponseRedirect("/")
        elif btnshowchart == "Show Chart":
            print(userid_val)
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            rows10 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select(select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Coin' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Coin' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Coin' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Coin' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Coin' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            coin= curr.fetchall()
            coin=str(coin)
            coin=re.findall(r"'(.*?)'", coin, re.DOTALL)
            coin=coin[0]
            rows1 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Plain Gold' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Plain Gold' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Plain Gold' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Plain Gold' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Plain Gold' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            plain_gold= curr.fetchall()
            plain_gold=str(plain_gold)
            plain_gold=re.findall(r"'(.*?)'", plain_gold, re.DOTALL)
            plain_gold=plain_gold[0]
            rows2 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Studded Gold' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Studded Gold' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Studded Gold' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Studded Gold' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Studded Gold' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            studded_gold= curr.fetchall()
            studded_gold=str(studded_gold)
            studded_gold=re.findall(r"'(.*?)'", studded_gold, re.DOTALL)
            studded_gold=studded_gold[0]
            rows3 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Premium Gold' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Premium Gold' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Premium Gold' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Premium Gold' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Premium Gold' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            premium_gold= curr.fetchall()
            premium_gold=str(premium_gold)
            premium_gold=re.findall(r"'(.*?)'", premium_gold, re.DOTALL)
            premium_gold=premium_gold[0]
            rows4 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Diamond Jewellery' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Diamond Jewellery' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Diamond Jewellery' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Diamond Jewellery' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Diamond Jewellery' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            diamond_jewellery= curr.fetchall()
            diamond_jewellery=str(diamond_jewellery)
            diamond_jewellery=re.findall(r"'(.*?)'", diamond_jewellery, re.DOTALL)
            diamond_jewellery=diamond_jewellery[0]
            rows5 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Solitaire Jewellery' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Solitaire Jewellery' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Solitaire Jewellery' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Solitaire Jewellery' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Solitaire Jewellery' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            solitaire_jewellery= curr.fetchall()
            solitaire_jewellery=str(solitaire_jewellery)
            solitaire_jewellery=re.findall(r"'(.*?)'", solitaire_jewellery, re.DOTALL)
            solitaire_jewellery=solitaire_jewellery[0]
            rows6 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Loose Diamonds' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Loose Diamonds' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Loose Diamonds' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Loose Diamonds' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Loose Diamonds' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            loose_diamonds= curr.fetchall()
            loose_diamonds=str(loose_diamonds)
            loose_diamonds=re.findall(r"'(.*?)'", loose_diamonds, re.DOTALL)
            loose_diamonds=loose_diamonds[0]
            rows7 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Jadau' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Jadau' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Jadau' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Jadau' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Jadau' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            jadau= curr.fetchall()
            jadau=str(jadau)
            jadau=re.findall(r"'(.*?)'", jadau, re.DOTALL)
            jadau=jadau[0]
            rows8 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Silver' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Silver' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Silver' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Silver' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Silver' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            silver= curr.fetchall()
            silver=str(silver)
            silver=re.findall(r"'(.*?)'", silver, re.DOTALL)
            silver=silver[0]
            rows9 = curr.execute("select ifnull(Total1,0)+ ifnull(Total2,0)+ ifnull(Total3,0)+ ifnull(Total4,0) +ifnull(Total5,0) as total from (select (select sum(TotalWeight) from CategoryDetails where UserId = '{}' and 'Platinum' in (Category1)) as Total1,(select sum(TotalWeight2) from CategoryDetails where UserId = '{}' and 'Platinum' in (Category2)) as Total2,(select sum(TotalWeight3) from CategoryDetails where UserId = '{}' and 'Platinum' in (Category3)) as Total3,(select sum(TotalWeight4) from CategoryDetails where UserId = '{}' and 'Platinum' in (Category4)) as Total4,(select sum(TotalWeight5) from CategoryDetails where UserId = '{}' and 'Platinum' in (Category5)) as Total5) as t".format(userid_val,userid_val,userid_val,userid_val,userid_val))
            platinum= curr.fetchall()
            platinum=str(platinum)
            platinum=re.findall(r"'(.*?)'", platinum, re.DOTALL)
            platinum=platinum[0]
            print(plain_gold)
            print(studded_gold)
            print(premium_gold)
            print(diamond_jewellery)
            print(solitaire_jewellery)
            print(loose_diamonds)
            print(jadau)
            print(silver)
            print(platinum)
            print(coin)
            fig, ax = plt.subplots(figsize=(10, 5), subplot_kw=dict(aspect="equal"))
            # recipe = ["375 g Coin",
            #           "75 g Jadau",
            #           "250 g Silver",
            #           "300 g Platinum"]
            recipe = ["{} g PlainGold".format(plain_gold),
                      "{} g StuddedGold".format(studded_gold),
                      "{} g PremiumGold".format(premium_gold),
                      "{} g DiamondJewellery".format(diamond_jewellery),
                      "{} g SolitaireJewellery".format(solitaire_jewellery),
                      "{} g LooseDiamonds".format(loose_diamonds),
                      "{} g Jadau".format(jadau),
                      "{} g Silver".format(silver),
                      "{} g Platinum".format(platinum),
                      "{} g Coin".format(coin)]
            data = [float(x.split()[0]) for x in recipe]
            category = [x.split()[-1] for x in recipe]

            def func(pct, allvals):
                absolute = int(pct/100.*np.sum(allvals))
                return "{:.1f}%\n({:d} g)".format(pct, absolute)

            wedges, texts, autotexts = ax.pie(data, autopct=lambda pct: func(pct, data),
                                              textprops=dict(color="w"))
            ax.legend(wedges, category,
                      title="Category",
                      loc="center left",
                      bbox_to_anchor=(1, 0, 0.5, 1))
            plt.setp(autotexts, size=8, weight="bold")
            ax.set_title("TBZ Sales Chart!")
            plt.savefig("static/images/plot.png")
            return HttpResponseRedirect("/charts")
        
        elif backbtn == "Back":
            print("INSIDE BACKKKKKKKKKKK")
            return render(request,"demo2.html",{'form_no':form_no,'today_dt':today_dt,'branch_val':branch_val})
        elif findbtn == "Find" and txtCustMob=="":
            return render(request,"demo2.html",{'form_no':form_no,'today_dt':today_dt,'branch_val':branch_val})
        elif findbtn == "Find" and txtCustMob!="":
            print("FETCHING MOBILE DATA!!!!!!!!!!!!")
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            rows = curr.execute("SELECT * FROM EntryDetails WHERE MobileNumber = '{}'".format(txtCustMob))
            db_output_list = curr.fetchall()
            db_tuple=tuple(db_output_list)
            len_tuple=len(db_tuple)
            print("INSIDE FIND MOBBBBBBBBBBB")
            print(db_tuple)
            return render(request,"new.html",{'db_tuple':db_tuple})
        elif btndwnldrep == 'Submit' and txtfromdate is not None and txttodate is not None:
            print("FETCHING DOWNLOAD REP DATA1!!!!!!!!!!!!")
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            rows = curr.execute("SELECT * FROM EntryDetails WHERE Date BETWEEN '{}' and '{}' and UserId = '{}'".format(txtfromdate,txttodate,userid_val))
            db_output_list = curr.fetchall()
            db_tuple=tuple(db_output_list)
            len_tuple=len(db_tuple)
            print("INSIDE DOWNLOAD REPPPPPP1")
            print(db_tuple)
            return render(request,"new.html",{'db_tuple':db_tuple})
        elif btndwnldrep == 'Submit' and txtfiltermob is not None:
            print("FETCHING DOWNLOAD REP DATA2!!!!!!!!!!!!")
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            rows = curr.execute("SELECT * FROM EntryDetails WHERE MobileNumber = '{}' and UserId = '{}'".format(txtfiltermob,userid_val))
            db_output_list = curr.fetchall()
            db_tuple=tuple(db_output_list)
            len_tuple=len(db_tuple)
            print("INSIDE DOWNLOAD REPPPPPP2")
            print(db_tuple)
            return render(request,"new.html",{'db_tuple':db_tuple})
        elif submitbtn == "Submit" and txtMobile != "" and txtCustName != "" and ddlNewOldCust != "" and ddlGender != "" and ddlAgeGroup != "" and ddlTBZ != "":
            print("REQUEST.POSTTTTTTTTTTTT")
            print(request.POST)
            txtFormNo=form_no
            ddlItem=request.POST.get('ddlItem')
            if ddlItem is None:
                ddlItem=""
            ddlItem1=request.POST.get('ddlItem1')
            if ddlItem1 is None:
                ddlItem1=""
            ddlItem2=request.POST.get('ddlItem2')
            if ddlItem2 is None:
                ddlItem2=""
            ddlItem3=request.POST.get('ddlItem3')
            if ddlItem3 is None:
                ddlItem3=""
            ddlItem4=request.POST.get('ddlItem4')
            if ddlItem4 is None:
                ddlItem4=""
            ddlWalkoutReason=request.POST.get('ddlWalkoutReason')
            if ddlWalkoutReason is None:
                ddlWalkoutReason=""
            ddlWalkoutReason1=request.POST.get('ddlWalkoutReason1')
            if ddlWalkoutReason1 is None:
                ddlWalkoutReason1=""
            ddlWalkoutReason2=request.POST.get('ddlWalkoutReason2')
            if ddlWalkoutReason2 is None:
                ddlWalkoutReason2=""
            ddlWalkoutReason3=request.POST.get('ddlWalkoutReason3')
            if ddlWalkoutReason3 is None:
                ddlWalkoutReason3=""
            ddlWalkoutReason4=request.POST.get('ddlWalkoutReason4')
            if ddlWalkoutReason4 is None:
                ddlWalkoutReason4=""
            txtEmail=request.POST.get('txtEmail')
            txtMailAddr1=request.POST.get('txtMailAddr1')
            txtMailAddr2=request.POST.get('txtMailAddr2')
            txtMailAddr3=request.POST.get('txtMailAddr3')
            txtCity=request.POST.get('txtCity')
            txtPin=request.POST.get('txtPin')
            # txtDate=request.POST.get('txtDate')
            txtDate=dt.strptime(today_dt, "%d-%m-%Y").date()
            # txtDate=request.POST.get('txtDate')
            txtSAName=request.POST.get('txtSAName')
            txtSAEmpId=request.POST.get('txtSAEmpId')
            txtDeptCRE=request.POST.get('txtDeptCRE')
            txtDeptEmpId=request.POST.get('txtDeptEmpId')
            txtTLName=request.POST.get('txtTLName')
            txtTLEmpId=request.POST.get('txtTLEmpId')
            txtRefName1=request.POST.get('txtRefName1')
            txtRefNumber1=request.POST.get('txtRefNumber1')
            txtRefName2=request.POST.get('txtRefName2')
            txtRefNumber2=request.POST.get('txtRefNumber2')
            ddlShwConversionStatus=request.POST.get('ddlShwConversionStatus')
            if ddlShwConversionStatus == 'Yes':
                ddlShwConversionStatus=1
            else:
                ddlShwConversionStatus=0
            ddlKPDone=request.POST.get('ddlKPDone')
            if ddlKPDone == 'Yes':
                ddlKPDone=1
            else:
                ddlKPDone=0
            txtNoofKP=request.POST.get('txtNoofKP')
            txtKPAmt=request.POST.get('txtKPAmt')
            txtBrandLoc=request.POST.get('txtBrandLoc')
            ddlOccassion=request.POST.get('ddlOccassion')
            ddlArea=request.POST.get('ddlArea')
            ddlCommunity=request.POST.get('ddlCommunity')
            ddlOccupation=request.POST.get('ddlOccupation')
            txtBirthday=request.POST.get('txtBirthday')
            txtAnniversary=request.POST.get('txtAnniversary')
            txtCustHobbies=request.POST.get('txtCustHobbies')
            ddlCategory=request.POST.get('ddlCategory')
            ddlItemConvStatus=request.POST.get('ddlItemConvStatus')
            txtReasonDetailed1=request.POST.get('txtReasonDetailed1')
            txtBudInGms=request.POST.get('txtBudInGms')
            txtTotSelWeight=request.POST.get('txtTotSelWeight')
            txtPurchaseAmt=request.POST.get('txtPurchaseAmt')
            ddlCategory1=request.POST.get('ddlCategory1')
            ddlItemConvStatus1=request.POST.get('ddlItemConvStatus1')
            txtReasonDetailed2=request.POST.get('txtReasonDetailed2')
            txtBudget1=request.POST.get('txtBudget1')
            txtTotalWt1=request.POST.get('txtTotalWt1')
            txtPurchaseAmt1=request.POST.get('txtPurchaseAmt1')
            ddlCategory2=request.POST.get('ddlCategory2')
            ddlItemConvStatus2=request.POST.get('ddlItemConvStatus2')
            txtReasonDetailed3=request.POST.get('txtReasonDetailed3')
            txtBudget2=request.POST.get('txtBudget2')
            txtTotalWt2=request.POST.get('txtTotalWt2')
            txtPurchaseAmt2=request.POST.get('txtPurchaseAmt2')
            ddlCategory3=request.POST.get('ddlCategory3')
            ddlItemConvStatus3=request.POST.get('ddlItemConvStatus3')
            txtReasonDetailed4=request.POST.get('txtReasonDetailed4')
            txtBudget3=request.POST.get('txtBudget3')
            txtTotalWt3=request.POST.get('txtTotalWt3')
            txtPurchaseAmt3=request.POST.get('txtPurchaseAmt3')
            ddlCategory4=request.POST.get('ddlCategory4')
            ddlItemConvStatus4=request.POST.get('ddlItemConvStatus4')
            txtReasonDetailed5=request.POST.get('txtReasonDetailed5')
            txtBudget4=request.POST.get('txtBudget4')
            txtTotalWt4=request.POST.get('txtTotalWt4')
            txtPurchaseAmt4=request.POST.get('txtPurchaseAmt4')
            txtDetails=request.POST.get('txtDetails')
            txtPrefJeweller=request.POST.get('txtPrefJeweller')
            ddlMOFC=request.POST.get('ddlMOFC')
            txtOtherSpecify=request.POST.get('txtOtherSpecify')
            ddlReasonKPWalkout=request.POST.get('ddlReasonKPWalkout')
            txtBranchName=request.POST.get('txtBranchName')
            txtBudInGms1=request.POST.get('txtBudInGms1')
            txtBudInGms2=request.POST.get('txtBudInGms2')
            txtBudInGms3=request.POST.get('txtBudInGms3')
            txtBudInGms4=request.POST.get('txtBudInGms4')
            txtBudInGms5=request.POST.get('txtBudInGms5')
            txtTotSelWeight1=request.POST.get('txtTotSelWeight1')
            txtTotSelWeight2=request.POST.get('txtTotSelWeight2')
            txtTotSelWeight3=request.POST.get('txtTotSelWeight3')
            txtTotSelWeight4=request.POST.get('txtTotSelWeight4')
            txtTotSelWeight5=request.POST.get('txtTotSelWeight5')
            txtLostGained1=request.POST.get('txtLostGained1')
            txtLostGained2=request.POST.get('txtLostGained2')
            txtLostGained3=request.POST.get('txtLostGained3')
            txtLostGained4=request.POST.get('txtLostGained4')
            txtLostGained5=request.POST.get('txtLostGained5')
            txtPurchaseAmt1=request.POST.get('txtPurchaseAmt1')
            txtPurchaseAmt2=request.POST.get('txtPurchaseAmt2')
            txtPurchaseAmt3=request.POST.get('txtPurchaseAmt3')
            txtPurchaseAmt4=request.POST.get('txtPurchaseAmt4')
            txtPurchaseAmt5=request.POST.get('txtPurchaseAmt5')
            print("BEFOREEEE")
            print(txtBudInGms1)
            print(txtLostGained1)
            if ddlItemConvStatus == "":
                ddlItemConvStatus =0
            if ddlItemConvStatus1 == "":
                ddlItemConvStatus1 =0
            if ddlItemConvStatus2 == "":
                ddlItemConvStatus2 =0
            if ddlItemConvStatus3 == "":
                ddlItemConvStatus3 =0
            if ddlItemConvStatus4 == "":
                ddlItemConvStatus4 =0
            if txtBudInGms1 == "":
                txtBudInGms1 =0
            if txtBudInGms2 == "":
                txtBudInGms2 =0
            if txtBudInGms3 == "":
                txtBudInGms3 =0
            if txtBudInGms4 == "":
                txtBudInGms4 =0
            if txtBudInGms5 == "":
                txtBudInGms5 =0
            if txtTotSelWeight1 == "":
                txtTotSelWeight1 =0
            if txtTotSelWeight2 == "":
                txtTotSelWeight2 =0
            if txtTotSelWeight3 == "":
                txtTotSelWeight3 =0
            if txtTotSelWeight4 == "":
                txtTotSelWeight4 =0
            if txtTotSelWeight5 == "":
                txtTotSelWeight5 =0
            if txtPurchaseAmt1 == "":
                txtPurchaseAmt1 =0
            if txtPurchaseAmt2 == "":
                txtPurchaseAmt2 =0
            if txtPurchaseAmt3 == "":
                txtPurchaseAmt3 =0
            if txtPurchaseAmt4 == "":
                txtPurchaseAmt4 =0
            if txtPurchaseAmt5 == "":
                txtPurchaseAmt5 =0
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            print("INSERTING DATAAAAAAAAAA")
            print(form_no)
            print(txtFormNo)
            print("AFTERRRRRRRRRRR")
            print(txtBudInGms1)
            print(txtLostGained1)
            print(userid_val)
            # rows = curr.execute("INSERT INTO Details.dbo.EntryDetails(FormNo,UserId,BranchName,MobileNumber,CustomerName,NewOldCustomer,BrandLocation,Community,Occupation,Gender,Age,Birthday,Anniversary,Occassion,Area,EmailId,KP_Amount,ShowroomConvSta,KP_Done,Address1,Address2,Address3,City,Pincode,Date,SAName,SAEmpId,DeptCRE,DeptEmpId,TLName,TLEmpId,RefData1,RefNumber1,RefData2,RefNumber2,NoOfKP,KPWalkoutReason,PreferredJew,Hobbies,KnowTBZ,Details,ModeOfComm,OtherMOCReason) VALUES('{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}')".format('12345','12345',txtBranchName,txtMobile,txtCust,ddlNewOldCust,txtBrandLoc,ddlCommunity,ddlOccupation,ddlGender,ddlAgeGroup,txtBirthday,txtAnniversary,ddlOccassion,ddlArea,txtEmail,txtKPAmt,ddlShwConversionStatus,'0',txtMailAddr1,txtMailAddr2,txtMailAddr3,txtCity,txtPin,txtDate,txtSAName,txtSAEmpId,txtDeptCRE,txtDeptEmpId,txtTLName,txtTLEmpId,txtRefName1,txtRefNumber1,txtRefName2,txtRefNumber2,txtNoofKP,ddlReasonKPWalkout,txtPrefJeweller,txtCustHobbies,ddlTBZ,txtDetails,ddlMOFC,txtOtherSpecify))
            # rows1 = curr.execute("CALL insertEntryDetails(txtFormNo,'{}','Ahmedabad',txtMobile,txtCustName,ddlNewOldCust,txtBrandLoc,ddlCommunity,ddlOccupation,ddlGender,ddlAgeGroup,txtBirthday,txtAnniversary,ddlOccassion,ddlArea,txtEmail,txtKPAmt,ddlShwConversionStatus,ddlKPDone,txtMailAddr1,txtMailAddr2,txtMailAddr3,txtCity,txtPin,txtDate,txtSAName,txtSAEmpId,txtDeptCRE,txtDeptEmpId,txtTLName,txtTLEmpId,txtRefName1,txtRefNumber1,txtRefName2,txtRefNumber2,txtNoofKP,ddlReasonKPWalkout,txtPrefJeweller,txtCustHobbies,ddlTBZ,txtDetails,ddlMOFC,txtOtherSpecify)")
            rows1 = curr.execute("CALL insertEntryDetails('{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}')".format(txtFormNo,userid_val,branch_val,txtMobile,txtCustName,ddlNewOldCust,txtBrandLoc,ddlCommunity,ddlOccupation,ddlGender,ddlAgeGroup,txtBirthday,txtAnniversary,ddlOccassion,ddlArea,txtEmail,txtKPAmt,ddlShwConversionStatus,ddlKPDone,txtMailAddr1,txtMailAddr2,txtMailAddr3,txtCity,txtPin,txtDate,txtSAName,txtSAEmpId,txtDeptCRE,txtDeptEmpId,txtTLName,txtTLEmpId,txtRefName1,txtRefNumber1,txtRefName2,txtRefNumber2,txtNoofKP,ddlReasonKPWalkout,txtPrefJeweller,txtCustHobbies,ddlTBZ,txtDetails,ddlMOFC,txtOtherSpecify))
            print("ONEEEEEEEEEE")
            rows2 = curr.execute("CALL insertCatDetails ('{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}')".format(txtFormNo,userid_val,ddlCategory,ddlItem,ddlItemConvStatus,ddlWalkoutReason,txtReasonDetailed1,txtBudInGms1,txtTotSelWeight1,txtLostGained1,txtPurchaseAmt1,ddlCategory1,ddlItem1,ddlItemConvStatus1,ddlWalkoutReason1,txtReasonDetailed2,txtBudInGms2,txtTotSelWeight2,txtLostGained2,txtPurchaseAmt2,ddlCategory2,ddlItem2,ddlItemConvStatus2,ddlWalkoutReason2,txtReasonDetailed3,txtBudInGms3,txtTotSelWeight3,txtLostGained3,txtPurchaseAmt3,ddlCategory3,ddlItem3,ddlItemConvStatus3,ddlWalkoutReason3,txtReasonDetailed4,txtBudInGms4,txtTotSelWeight4,txtLostGained4,txtPurchaseAmt4,ddlCategory4,ddlItem4,ddlItemConvStatus4,ddlWalkoutReason4,txtReasonDetailed5,txtBudInGms5,txtTotSelWeight5,txtLostGained5,txtPurchaseAmt5))
            conn.commit()
            form_no=form_no+1
            return render(request,"demo2.html",{'form_no':form_no,'today_dt':today_dt,'branch_val':branch_val})
        else:
            return render(request,"demo2.html",{'form_no':form_no,'today_dt':today_dt,'branch_val':branch_val})
    else:
        return render(request,"login.html")

def personalinfo(request):
    print("INSIDE personalinfo")
    return render(request,"personalinfo.html")

def productinfo(request):
    print("INSIDE productinfo")
    return render(request,"productinfo.html")

def additionalinfo(request):
    print("INSIDE additionalinfo")
    return render(request,"additionalinfo.html")

def register(request):
    print("INSIDE REGISTERRRRRRRRRRRRR")
    return render(request,"register.html")

def ChangePassword(request):
    print("INSIDE Change Password")
    btnchgpasswd=request.POST.get('btnchgpasswd')
    if request.method == 'POST':
        if btnchgpasswd == 'Change Password':
            print("REQUEST.POSTTTTTTTTTTTT2")
            txtUserID=request.POST.get('txtUserID')
            txtNewPwd=request.POST.get('txtNewPwd')
            conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
            curr = conn.cursor()
            print("UPDATING DATAAAAAAAAAA")
            rows = curr.execute("UPDATE branch SET py_passwd = '{}' WHERE UserId = '{}'".format(txtNewPwd,txtUserID))
            conn.commit()
            return HttpResponseRedirect("/")     
        else:
            return HttpResponseRedirect("/")
    else:
        return render(request,"ChangePassword.html")

def demo(request):
    print("INSIDE DEMOOOOOOOOOOOOOO")
    if request.method == 'POST':
        print("REQUEST.POSTTTTTTTTTTTT")
        txtFormNo=request.POST.get('txtFormNo')
        txtCust=request.POST.get('txtCust')
        txtEmail=request.POST.get('txtEmail')
        txtMailAddr1=request.POST.get('txtMailAddr1')
        txtMailAddr2=request.POST.get('txtMailAddr2')
        txtMailAddr3=request.POST.get('txtMailAddr3')
        txtCity=request.POST.get('txtCity')
        txtPin=request.POST.get('txtPin')
        # txtDate=request.POST.get('txtDate')
        txtDate=dt.now()
        txtSAName=request.POST.get('txtSAName')
        txtSAEmpId=request.POST.get('txtSAEmpId')
        txtDeptCRE=request.POST.get('txtDeptCRE')
        txtDeptEmpId=request.POST.get('txtDeptEmpId')
        txtTLName=request.POST.get('txtTLName')
        txtTLEmpId=request.POST.get('txtTLEmpId')
        txtRefName1=request.POST.get('txtRefName1')
        txtRefNumber1=request.POST.get('txtRefNumber1')
        txtRefName2=request.POST.get('txtRefName2')
        txtRefNumber2=request.POST.get('txtRefNumber2')
        ddlShwConversionStatus=request.POST.get('ddlShwConversionStatus')
        if ddlShwConversionStatus == 'Yes':
            ddlShwConversionStatus=1
        else:
            ddlShwConversionStatus=0
        ddlKPDone=request.POST.get('ddlKPDone')
        if ddlKPDone == 'Yes':
            ddlKPDone=1
        else:
            ddlKPDone=0
        txtNoofKP=request.POST.get('txtNoofKP')
        txtKPAmt=request.POST.get('txtKPAmt')
        txtMobile=request.POST.get('txtMobile')
        txtCustName=request.POST.get('txtCustName')
        ddlNewOldCust=request.POST.get('ddlNewOldCust')
        txtBrandLoc=request.POST.get('txtBrandLoc')
        ddlOccassion=request.POST.get('ddlOccassion')
        ddlArea=request.POST.get('ddlArea')
        ddlCommunity=request.POST.get('ddlCommunity')
        ddlOccupation=request.POST.get('ddlOccupation')
        ddlGender=request.POST.get('ddlGender')
        ddlAgeGroup=request.POST.get('ddlAgeGroup')
        txtBirthday=request.POST.get('txtBirthday')
        txtAnniversary=request.POST.get('txtAnniversary')
        txtCustHobbies=request.POST.get('txtCustHobbies')
        ddlCategory=request.POST.get('ddlCategory')
        ddlItemConvStatus=request.POST.get('ddlItemConvStatus')
        txtReasonDetailed1=request.POST.get('txtReasonDetailed1')
        txtBudInGms=request.POST.get('txtBudInGms')
        txtTotSelWeight=request.POST.get('txtTotSelWeight')
        txtPurchaseAmt=request.POST.get('txtPurchaseAmt')
        ddlCategory1=request.POST.get('ddlCategory1')
        ddlItemConvStatus1=request.POST.get('ddlItemConvStatus1')
        txtReasonDetailed2=request.POST.get('txtReasonDetailed2')
        txtBudget1=request.POST.get('txtBudget1')
        txtTotalWt1=request.POST.get('txtTotalWt1')
        txtPurchaseAmt1=request.POST.get('txtPurchaseAmt1')
        ddlCategory2=request.POST.get('ddlCategory2')
        ddlItemConvStatus2=request.POST.get('ddlItemConvStatus2')
        txtReasonDetailed3=request.POST.get('txtReasonDetailed3')
        txtBudget2=request.POST.get('txtBudget2')
        txtTotalWt2=request.POST.get('txtTotalWt2')
        txtPurchaseAmt2=request.POST.get('txtPurchaseAmt2')
        ddlCategory3=request.POST.get('ddlCategory3')
        ddlItemConvStatus3=request.POST.get('ddlItemConvStatus3')
        txtReasonDetailed4=request.POST.get('txtReasonDetailed4')
        txtBudget3=request.POST.get('txtBudget3')
        txtTotalWt3=request.POST.get('txtTotalWt3')
        txtPurchaseAmt3=request.POST.get('txtPurchaseAmt3')
        ddlCategory4=request.POST.get('ddlCategory4')
        ddlItemConvStatus4=request.POST.get('ddlItemConvStatus4')
        txtReasonDetailed5=request.POST.get('txtReasonDetailed5')
        txtBudget4=request.POST.get('txtBudget4')
        txtTotalWt4=request.POST.get('txtTotalWt4')
        txtPurchaseAmt4=request.POST.get('txtPurchaseAmt4')
        ddlTBZ=request.POST.get('ddlTBZ')
        txtDetails=request.POST.get('txtDetails')
        txtPrefJeweller=request.POST.get('txtPrefJeweller')
        ddlMOFC=request.POST.get('ddlMOFC')
        txtOtherSpecify=request.POST.get('txtOtherSpecify')
        ddlReasonKPWalkout=request.POST.get('ddlReasonKPWalkout')
        txtBranchName=request.POST.get('txtBranchName')
        txtBranchName=request.POST.get('txtBranchName')
        conn = pymysql.connect(db_host, db_user, db_pwd, db_database)
        curr = conn.cursor()
        print("INSERTING DATAAAAAAAAAA")
        # rows = curr.execute("INSERT INTO Details.dbo.EntryDetails(FormNo,UserId,BranchName,MobileNumber,CustomerName,NewOldCustomer,BrandLocation,Community,Occupation,Gender,Age,Birthday,Anniversary,Occassion,Area,EmailId,KP_Amount,ShowroomConvSta,KP_Done,Address1,Address2,Address3,City,Pincode,Date,SAName,SAEmpId,DeptCRE,DeptEmpId,TLName,TLEmpId,RefData1,RefNumber1,RefData2,RefNumber2,NoOfKP,KPWalkoutReason,PreferredJew,Hobbies,KnowTBZ,Details,ModeOfComm,OtherMOCReason) VALUES('{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}','{}')".format('12345','12345',txtBranchName,txtMobile,txtCust,ddlNewOldCust,txtBrandLoc,ddlCommunity,ddlOccupation,ddlGender,ddlAgeGroup,txtBirthday,txtAnniversary,ddlOccassion,ddlArea,txtEmail,txtKPAmt,ddlShwConversionStatus,'0',txtMailAddr1,txtMailAddr2,txtMailAddr3,txtCity,txtPin,txtDate,txtSAName,txtSAEmpId,txtDeptCRE,txtDeptEmpId,txtTLName,txtTLEmpId,txtRefName1,txtRefNumber1,txtRefName2,txtRefNumber2,txtNoofKP,ddlReasonKPWalkout,txtPrefJeweller,txtCustHobbies,ddlTBZ,txtDetails,ddlMOFC,txtOtherSpecify))
        rows = curr.execute("EXECUTE dbo.insertEntryDetails @formNo = '{}',@uid = '{}',@branchName = '{}',@mobileNo = '{}',@custName = '{}',@newoldCust = '{}',@brandLocation = '{}',@community = '{}',@occupation = '{}',@gender = '{}',@age = '{}',@birthday = '{}',@anniversary = '{}',@occassion = '{}',@area = '{}',@email = '{}',@kpAmount = '{}',@showconvsta = '{}',@kpdone = '{}',@address1 = '{}',@address2 = '{}',@address3 = '{}',@city = '{}',@pincode = '{}',@enterdate = '{}',@saname = '{}',@saempid = '{}',@deptcre = '{}',@deptempid = '{}',@tlname = '{}',@tlempid = '{}',@refData1 = '{}',@refNumber1 = '{}',@refData2 = '{}',@refNumber2 = '{}',@noofkp = '{}',@kpwalkoutreason = '{}',@prefJew = '{}',@hobbies = '{}',@knowTbz = '{}',@details = '{}',@mofc = '{}',@otherMOCReason = '{}'".format('123456','123456',txtBranchName,txtMobile,txtCust,ddlNewOldCust,txtBrandLoc,ddlCommunity,ddlOccupation,ddlGender,ddlAgeGroup,txtBirthday,txtAnniversary,ddlOccassion,ddlArea,txtEmail,txtKPAmt,ddlShwConversionStatus,ddlKPDone,txtMailAddr1,txtMailAddr2,txtMailAddr3,txtCity,txtPin,txtDate,txtSAName,txtSAEmpId,txtDeptCRE,txtDeptEmpId,txtTLName,txtTLEmpId,txtRefName1,txtRefNumber1,txtRefName2,txtRefNumber2,txtNoofKP,ddlReasonKPWalkout,txtPrefJeweller,txtCustHobbies,ddlTBZ,txtDetails,ddlMOFC,txtOtherSpecify))
        conn.commit()
        print("DATA INSERTED SUCCESSFULLY")
        return render(request,"demo.html")
    # else:
    #     return render(request,"demo.html")


NEW/settings.py

"""
Django settings for TBZNEW project.

Generated by 'django-admin startproject' using Django 2.1.

For more information on this file, see
https://docs.djangoproject.com/en/2.1/topics/settings/

For the full list of settings and their values, see
https://docs.djangoproject.com/en/2.1/ref/settings/
"""

import os

# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
TEMPLATE_DIR = os.path.join(BASE_DIR,"templates")

# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = 'o%jo633=d99^p7_al!sx1g)47yd&x3-dp&4vb4u_kq3rtf$^wq'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

ALLOWED_HOSTS = []


# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'basicapp',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'TBZNEW.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [TEMPLATE_DIR],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'TBZNEW.wsgi.application'


# Database
# https://docs.djangoproject.com/en/2.1/ref/settings/#databases

DATABASES = {
    'default': {
        
    }
}


# Password validation
# https://docs.djangoproject.com/en/2.1/ref/settings/#auth-password-validators

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


# Internationalization
# https://docs.djangoproject.com/en/2.1/topics/i18n/

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True


# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/2.1/howto/static-files/

STATIC_URL = '/static/'

STATICFILES_DIRS =[
        os.path.join(BASE_DIR,"static"),
]





NEW/urls.py

"""TBZNEW URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path
from basicapp import views

urlpatterns = [
    path('', views.login, name='login'),
    path('EntryDetails/',views.EntryDetails,name='EntryDetails'),
    path('register/', views.register, name='register'),
    path('charts/', views.charts, name='charts'),
    path('ChangePassword/', views.ChangePassword, name='ChangePassword'),
    path('personalinfo/', views.personalinfo, name='personalinfo'),
    path('productinfo/', views.productinfo, name='productinfo'),
    path('additionalinfo/', views.additionalinfo, name='additionalinfo'),
    path('demo/', views.demo, name='demo'),
    path('admin/', admin.site.urls),
]



changepassword.html


<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head><title>
</title>
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/javascript.util/0.12.12/javascript.util.min.js"></script>
  <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
  <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
    <script type="text/javascript">
        $(document).ready(function() {
        window.history.pushState(null, "", window.location.href);        
        window.onpopstate = function() {
        window.history.pushState(null, "", window.location.href);
        };
        });
        function valEmpty() {
            if ($('#txtUserID').val() == "") {
                alert("UserId cannot be blank");
                $('#txtUserID').focus();
                return false;
            }
            else if ($('#txtNewPwd').val() == "") {
                alert("New password cannot be blank");
                $('#txtNewPwd').focus();
                return false;
            }
            else if ($('#txtCfgPwd').val() == "") {
                alert("Confirm password cannot be blank");
                $('#txtCfgPwd').focus();
                return false;
            }
            else if (($('#txtNewPwd').val() != "") && ($('#txtCfgPwd').val() != "")) {
                if ($('#txtNewPwd').val() != $('#txtCfgPwd').val()) {
                    alert("Your new password does not match the confirm password!");
                    $('#txtCfgPwd').val('');
                    $('#txtCfgPwd').focus();
                    return false;
                }
                else {
                    alert("Password changed successfully")
                    return changepwdform.submit();
                }
            }
        }
    </script>
</head>
<body>
<div class="container" style="font-size:12px">
  <div class="jumbotron">
    <form method="post" action="" id="changepwdform">
        {% csrf_token %}
        <span id="lblUserID"style="padding-right: 57px;">User ID</span>
        <input name="txtUserID" type="text" id="txtUserID" placeHolder="Enter User ID" /><br><br>
        <span id="lblOldPwd"style="padding-right: 23px;">Old Password</span>
        <input name="txtOldPwd" type="password" id="txtOldPwd" placeholder="Enter Old Password"/><br><br>
        <span id="lblNewPwd"style="padding-right: 19px;">New Password</span>
        <input name="txtNewPwd" type="password" id="txtNewPwd" placeholder="Enter New Password"/><br><br>
        <span id="lblCfgPwd">Confirm Password</span>
        <input name="txtCfgPwd" type="password" id="txtCfgPwd" placeholder="Enter Confirm Password"/><br><br>
        <br>
        <input type="submit" name="btnchgpasswd" id="btnchgpasswd" class="btn btn-primary" value="Change Password" onclick="valEmpty();" style="font-size:12px">
        <input type="submit" name="backbtn3" id="backbtn3" class="btn btn-primary" value="Back" onclick="this.form5.submit();" style="font-size:12px">
    </form>
  </div>
</div>
</body>
</html>


charts.html


<html>
{% load static %}
  <head>
    <title></title>
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script src="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/javascript.util/0.12.12/javascript.util.min.js"></script>
    <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
    <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
  </head>
  <body>
   <form class="form5" id="form5" action="/EntryDetails/" method="POST">
   {% csrf_token %}
    <div align="center">
	    <img src="{% static "images/plot.png" %}" alt="Image Placeholder"><br>
	    <input type="submit" name="backbtn2" id="backbtn2" class="btn btn-primary" value="Back " onclick="this.form5.submit();" style="font-size:12px">
	</div>
   </form>
  </body>
</html>



demo2.html

<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script src="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/javascript.util/0.12.12/javascript.util.min.js"></script>
    <link rel="stylesheet" href="https://code.jquery.com/ui/1.12.0/themes/smoothness/jquery-ui.css">
    <script src="https://code.jquery.com/ui/1.12.0/jquery-ui.min.js"></script>
    <script>
     //<![CDATA[
     $(document).ready(function() {
          debugger;
          $("textarea").text("");
          document.getElementById("datepicker3").value = ""
          document.getElementById("datepicker4").value = ""
          document.getElementById("txtfiltermob").value = ""
          document.getElementById("datepicker3").disabled = true;
          document.getElementById("datepicker4").disabled = true;
          document.getElementById("txtfiltermob").disabled = true;
          window.history.pushState(null, "", window.location.href)
          window.onpopstate = function() {
          window.history.pushState(null, "", window.location.href)
        }
      });
      function validateEmail(emailField){
        var reg = /^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/;
        if (emailField.value != "") {
          if (reg.test(emailField.value) == false) 
          {
              alert('Invalid Email Address');
              return false;
          }
        }
      }
      function validateName(nameField){
        debugger;
        var reg = /^[A-Za-z\ ]+$/;
        if (nameField.value != "") {
          if (reg.test(nameField.value) == false) 
          {
              alert('Invalid Name');
              return false;
          }
        }
      }
      function validateCity(cityField){
        debugger;
        var reg = /^[A-Za-z\ ]+$/;
        if (cityField.value != "") {
          if (reg.test(cityField.value) == false) 
          {
              alert('Invalid City');
              return false;
          }
        }
      }
      function validatePin(pinField){
        debugger;
        var reg = /^\d{6}$/;
        if (pinField.value != "") {
          if (reg.test(pinField.value) == false) 
          {
              alert('Invalid Pin Code');
              return false;
          }
        }
      }
      function validateMobile(mobileField){
        var reg = /^([7-9]{1})+([0-9]{9})$/;
        if (mobileField.value != "") {
          if (reg.test(mobileField.value) == false) 
          {
              alert('Invalid Mobile Number');
              return false;
          }
        }
      }
      function validateDate(dateField){
        debugger;
        var reg = /^\d{4}[\-](0?[1-9]|1[012])[\-](0?[1-9]|[12][0-9]|3[01])$/;
        if (dateField.value != "") {
          if (reg.test(dateField.value) == false) 
          {
              alert('Invalid Date format!');
              return false;
          }
        }
      }
     function downmethchange(selectObj) {
        debugger;
        var idx = selectObj.selectedIndex;
        var which = selectObj.options[idx].value;
        if (which == ''){
          document.getElementById("datepicker3").value = ""
          document.getElementById("datepicker4").value = ""
          document.getElementById("txtfiltermob").value = ""
          document.getElementById("datepicker3").disabled = true;
          document.getElementById("datepicker4").disabled = true;
          document.getElementById("txtfiltermob").disabled = true;
        }
        else if (which == 'Date'){
          document.getElementById("txtfiltermob").value = ""
          document.getElementById("datepicker3").disabled = false;
          document.getElementById("datepicker4").disabled = false;
          document.getElementById("txtfiltermob").disabled = true;
        }
        else if (which == 'Mobile Number'){
          document.getElementById("datepicker3").value = ""
          document.getElementById("datepicker4").value = ""
          document.getElementById("datepicker3").disabled = true;
          document.getElementById("datepicker4").disabled = true;
          document.getElementById("txtfiltermob").disabled = false;
        }
      }
      // $('#ddldwnldmethod').change(function () {
      //     if ($ddldwnldmethod.val() == 'Date') {
      //         $txtfiltermob.attr('disabled', 'disabled').val('');
      //     }
      // }).trigger('change');
      function clear_func1() {
        debugger;
        $("#txtEmail").val("");
        $("#txtMobile").val("");
        $("#txtCustName").val("");
        $("#txtBrandLoc").val("");
        $("#txtBirthday").val("");
        $("#txtAnniversary").val("");
        $("#txtMailAddr1").val("");
        $("#txtMailAddr2").val("");
        $("#txtMailAddr3").val("");
        $("#txtCity").val("");
        $("#txtPin").val("");
        $("#txtSAName").val("");
        $("#txtSAEmpId").val("");
        $("#txtDeptCRE").val("");
        $("#txtDeptEmpId").val("");
        $("#txtTLName").val("");
        $("#txtTLEmpId").val("");
        $("#txtRefName1").val("");
        $("#txtRefNumber1").val("");
        $("#txtRefName2").val("");
        $("#txtRefNumber2").val("");
        $("#txtCustHobbies").val("");
        $("#datepicker").val("");
        $("#datepicker2").val("");
        $('#ddlNewOldCust option:selected').text("--Select--");
        $('#ddlOccassion option:selected').text("--Select--");
        $('#ddlArea option:selected').text("--Select--");
        $('#ddlCommunity option:selected').text("--Select--");
        $('#ddlOccupation option:selected').text("--Select--");
        $('#ddlGender option:selected').text("--Select--");
        $('#ddlAgeGroup option:selected').text("--Select--");
      }
      function clear_func2() {
        debugger;
        $("#txtNoofKP").val("");
        $("#txtKPAmt").val("");
        $("#txtReasonDetailed1").val("");
        $("#txtReasonDetailed2").val("");
        $("#txtReasonDetailed3").val("");
        $("#txtReasonDetailed4").val("");
        $("#txtReasonDetailed5").val("");
        $("#txtPurchaseAmt1").val("");
        $("#txtPurchaseAmt2").val("");
        $("#txtPurchaseAmt3").val("");
        $("#txtPurchaseAmt4").val("");
        $("#txtPurchaseAmt5").val("");
        $("#txtLostGained1").val("");
        $("#txtLostGained2").val("");
        $("#txtLostGained3").val("");
        $("#txtLostGained4").val("");
        $("#txtLostGained5").val("");
        $("#txtTotSelWeight1").val("");
        $("#txtTotSelWeight2").val("");
        $("#txtTotSelWeight3").val("");
        $("#txtTotSelWeight4").val("");
        $("#txtTotSelWeight5").val("");
        $("#txtBudInGms1").val("");
        $("#txtBudInGms2").val("");
        $("#txtBudInGms3").val("");
        $("#txtBudInGms4").val("");
        $("#txtBudInGms5").val("");
        $('#ddlShwConversionStatus option:selected').text("--Select--");
        $('#ddlKPDone option:selected').text("--Select--");
        $('#ddlReasonKPWalkout option:selected').text("--Select--");
        $('#ddlCategory option:selected').text("--Select--");
        $('#ddlCategory1 option:selected').text("--Select--");
        $('#ddlCategory2 option:selected').text("--Select--");
        $('#ddlCategory3 option:selected').text("--Select--");
        $('#ddlCategory4 option:selected').text("--Select--");
        $('#ddlItem option:selected').text("--Select--");
        $('#ddlItem1 option:selected').text("--Select--");
        $('#ddlItem2 option:selected').text("--Select--");
        $('#ddlItem3 option:selected').text("--Select--");
        $('#ddlItem4 option:selected').text("--Select--");
        $('#ddlItemConvStatus option:selected').text("--Select--");
        $('#ddlItemConvStatus1 option:selected').text("--Select--");
        $('#ddlItemConvStatus2 option:selected').text("--Select--");
        $('#ddlItemConvStatus3 option:selected').text("--Select--");
        $('#ddlItemConvStatus4 option:selected').text("--Select--");
        $('#ddlWalkoutReason option:selected').text("--Select--");
        $('#ddlWalkoutReason1 option:selected').text("--Select--");
        $('#ddlWalkoutReason2 option:selected').text("--Select--");
        $('#ddlWalkoutReason3 option:selected').text("--Select--");
        $('#ddlWalkoutReason4 option:selected').text("--Select--");
      }
      function clear_func3() {
        debugger;
        $("#txtDetails").val("");
        $("#txtPrefJeweller").val("");
        $('#ddlTBZ option:selected').text("--Select--");
        $('#ddlMOFC option:selected').text("--Select--");
      }
      function download_rep() {
        debugger;
        var txtfromdate = $('#datepicker3').val();
        var txttodate = $('#datepicker4').val();
        var txtfiltermob = $('#txtfiltermob').val();
        if (txtfromdate == "" && txttodate == "" && txtfiltermob == "") {
          alert("Please enter details before submitting!");
          $('.nav-tabs > .active').next('li').next('li').find('a').trigger('click');
          $('#ddldwnldmethod').focus();
            return false;
        }
        else{
          this.form3.submit();
        }
      }
      function findMob() {
        debugger;
        var txtCustMob = $('#txtCustMob').val();
        if (txtCustMob == "") {
            alert("Please enter Mobile Number");
            return false;
        }
            this.form2.submit();
        }
      function calc_percent1() {
        debugger;
        var txtBudInGms = $('#txtBudInGms1').val();
        var txtTotSelWeight = $('#txtTotSelWeight1').val();
        if (txtBudInGms != "" && txtTotSelWeight != "") {
          percent_loss_gain=((txtTotSelWeight/txtBudInGms)-1).toFixed(3);
          document.getElementById("txtLostGained1").value = percent_loss_gain;
        }

      }
      function calc_percent2() {
        debugger;
        var txtBudInGms = $('#txtBudInGms2').val();
        var txtTotSelWeight = $('#txtTotSelWeight2').val();
        if (txtBudInGms != "" && txtTotSelWeight != "") {
          percent_loss_gain=((txtTotSelWeight/txtBudInGms)-1).toFixed(3);
          document.getElementById("txtLostGained2").value = percent_loss_gain;
        }

      }
      function calc_percent3() {
        debugger;
        var txtBudInGms = $('#txtBudInGms3').val();
        var txtTotSelWeight = $('#txtTotSelWeight3').val();
        if (txtBudInGms != "" && txtTotSelWeight != "") {
          percent_loss_gain=((txtTotSelWeight/txtBudInGms)-1).toFixed(3);
          document.getElementById("txtLostGained3").value = percent_loss_gain;
        }

      }
      function calc_percent4() {
        debugger;
        var txtBudInGms = $('#txtBudInGms4').val();
        var txtTotSelWeight = $('#txtTotSelWeight4').val();
        if (txtBudInGms != "" && txtTotSelWeight != "") {
          percent_loss_gain=((txtTotSelWeight/txtBudInGms)-1).toFixed(3);
          document.getElementById("txtLostGained4").value = percent_loss_gain;
        }

      }
      function calc_percent5() {
        debugger;
        var txtBudInGms = $('#txtBudInGms5').val();
        var txtTotSelWeight = $('#txtTotSelWeight5').val();
        if (txtBudInGms != "" && txtTotSelWeight != "") {
          percent_loss_gain=((txtTotSelWeight/txtBudInGms)-1).toFixed(3);
          document.getElementById("txtLostGained5").value = percent_loss_gain;
        }

      }
      function generate_table() {
        debugger;
        // get the reference for the body
        var custmob = $('#txtCustMob').val();

        var div = document.getElementsByTagName("div")[1];

        // creates a <table> element and a <tbody> element
        var tbl = document.createElement("table");
        var tblBody = document.createElement("tbody");

        // creating all cells
        for (var i = 0; i < 2; i++) {
          // creates a table row

          var row = document.createElement("tr");

          for (var j = 0; j < 2; j++) {
            // Create a <td> element and a text node, make the text
            // node the contents of the <td>, and put the <td> at
            // the end of the table row
            var cell = document.createElement("td");
            var cellText = document.createTextNode("cell in row "+i+", column "+j);
            cell.appendChild(cellText);
            row.appendChild(cell);
          }
          // add the row to the end of the table body
          tblBody.appendChild(row);
        }
        // put the <tbody> in the <table>
        tbl.appendChild(tblBody);
        // appends <table> into <body>
        div.appendChild(tbl);
        // sets the border attribute of tbl to 2;
        tbl.setAttribute("border", "2");
      }

      function valEmpty1(){
        debugger;
        var txtMobileNo = $('#txtMobile').val();
        var emailId = $('#txtEmail').val();
        var custName = $('#txtCustName').val();
        var newOldCust = $('#ddlNewOldCust option:selected').text();
        var gender = $('#ddlGender option:selected').text();
        var ageGroup = $('#ddlAgeGroup option:selected').text();
        if (txtMobileNo == "") {
            alert("Mobile Number cannot be blank");
            $('#txtMobile').focus();
            return false;
        }
        else if (custName == "") {
            alert("Customer Name cannot be blank");
            $('#txtCustName').focus();
            return false;
        }
        else if (newOldCust == "--Select--") {
            alert("Please select option for New/Old Customer");
            $('#ddlNewOldCust').focus();
            return false;
        }
        else if (gender == "--Select--") {
            alert("Please select option for Gender");
            $('#ddlGender').focus();
            return false;
        }
        else if (ageGroup == "--Select--") {
            alert("Please select option for Age Group");
            $('#ddlAgeGroup').focus();
            return false;
        }
        $('.nav-tabs > .active').next('li').find('a').trigger('click');  
      }

      function valEmpty2(){
            debugger;
            var kpDone = $('#ddlKPDone option:selected').text();
            var kpWalkoutReason = $('#ddlReasonKPWalkout option:selected').text();
            var kpNo = $('#txtNoofKP').val();
            var kpAmount = $('#txtKPAmt').val();
            var txtMobileNo = $('#txtMobile').val();
            var emailId = $('#txtEmail').val();
            var custName = $('#txtCustName').val();
            var newOldCust = $('#ddlNewOldCust option:selected').text();
            var gender = $('#ddlGender option:selected').text();
            var ageGroup = $('#ddlAgeGroup option:selected').text();
            if (txtMobileNo == "") {
                alert("Mobile Number cannot be blank");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#txtMobile').focus();
                return false;
            }
            else if (custName == "") {
                alert("Customer Name cannot be blank");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#txtCustName').focus();
                return false;
            }
            else if (newOldCust == "--Select--") {
                alert("Please select option for New/Old Customer");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#ddlNewOldCust').focus();
                return false;
            }
            else if (gender == "--Select--") {
                alert("Please select option for Gender");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#ddlGender').focus();
                return false;
            }
            else if (ageGroup == "--Select--") {
                alert("Please select option for Age Group");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#ddlAgeGroup').focus();
                return false;
            }
            if (kpDone == "--Select--") {
                alert("Please specify KP Done or Not");
                $('#ddlKPDone').focus();
                return false;
            }
            if (kpDone == "No") {
                if (kpWalkoutReason == "--Select--") {
                    alert("Please specify KP walkout Reason");
                    $('#ddlReasonKPWalkout').focus();
                    return false;
                }
            }
            if (kpDone == "Yes") {
                if ((kpNo == "") && (kpAmount == "")) {
                    alert("Please enter KPNumber and KPAmount");
                    $('#txtNoofKP').focus();
                    $('#txtKPAmt').focus();
                    return false;
                }
            }
            $('.nav-tabs > .active').next('li').next('li').find('a').trigger('click');  
      }

      function valEmpty3(){
          debugger;
          var knowtbz = $('#ddlTBZ option:selected').text();
          var kpDone = $('#ddlKPDone option:selected').text();
          var kpWalkoutReason = $('#ddlReasonKPWalkout option:selected').text();
          var kpNo = $('#txtNoofKP').val();
          var kpAmount = $('#txtKPAmt').val();
          var txtMobileNo = $('#txtMobile').val();
          var emailId = $('#txtEmail').val();
          var custName = $('#txtCustName').val();
          var newOldCust = $('#ddlNewOldCust option:selected').text();
          var gender = $('#ddlGender option:selected').text();
          var ageGroup = $('#ddlAgeGroup option:selected').text();
          if (txtMobileNo == "") {
                alert("Mobile Number cannot be blank");
                $('.nav-tabs > .active').find('a').trigger('click');
                $('#txtMobile').focus();
                return false;
            }
          else if (custName == "") {
              alert("Customer Name cannot be blank");
              $('.nav-tabs > .active').find('a').trigger('click');
              $('#txtCustName').focus();
              return false;
          }
          else if (newOldCust == "--Select--") {
              alert("Please select option for New/Old Customer");
              $('.nav-tabs > .active').find('a').trigger('click');
              $('#ddlNewOldCust').focus();
              return false;
          }
          else if (gender == "--Select--") {
              alert("Please select option for Gender");
              $('.nav-tabs > .active').find('a').trigger('click');
              $('#ddlGender').focus();
              return false;
          }
          else if (ageGroup == "--Select--") {
              alert("Please select option for Age Group");
              $('.nav-tabs > .active').find('a').trigger('click');
              $('#ddlAgeGroup').focus();
              return false;
          }
          if (kpDone == "--Select--") {
              alert("Please specify KP Done or Not");
              $('.nav-tabs > .active').next('li').find('a').trigger('click');
              $('#ddlKPDone').focus();
              return false;
          }
          if (kpDone == "No") {
              if (kpWalkoutReason == "--Select--") {
                  alert("Please specify KP walkout Reason");
                  $('.nav-tabs > .active').next('li').find('a').trigger('click');
                  $('#ddlReasonKPWalkout').focus();
                  return false;
              }
          }
          if (kpDone == "Yes") {
              if ((kpNo == "") && (kpAmount == "")) {
                  alert("Please enter KPNumber and KPAmount");
                  $('.nav-tabs > .active').next('li').find('a').trigger('click');
                  $('#txtNoofKP').focus();
                  $('#txtKPAmt').focus();
                  return false;
              }
          }
          if (knowtbz == "--Select--") {
              alert("Please specify How you got to know about TBZ?");
              $('#ddlTBZ').focus();
              return false;
          }
          document.getElementById("txtLostGained1").disabled = false;
          document.getElementById("txtLostGained2").disabled = false;
          document.getElementById("txtLostGained3").disabled = false;
          document.getElementById("txtLostGained4").disabled = false;
          document.getElementById("txtLostGained5").disabled = false;
          LostGained1=document.getElementById("txtLostGained1").value
          LostGained2=document.getElementById("txtLostGained2").value
          LostGained3=document.getElementById("txtLostGained3").value
          LostGained4=document.getElementById("txtLostGained4").value
          LostGained5=document.getElementById("txtLostGained5").value
          if (LostGained1 == ""){
              document.getElementById("txtLostGained1").value="0.000";
          }
          if (LostGained2 == ""){
              document.getElementById("txtLostGained2").value="0.000";
          }
          if (LostGained3 == ""){
              document.getElementById("txtLostGained3").value="0.000";
          }
          if (LostGained4 == ""){
              document.getElementById("txtLostGained4").value="0.000";
          }
          if (LostGained5 == ""){
              document.getElementById("txtLostGained5").value="0.000";
          }
          this.form1.submit();
          alert("Data submitted successfully!");
      }

     $( function() {
     $( "#datepicker" ).datepicker({
       changeMonth: true,
       changeYear: true,
       dateFormat: 'yy-mm-dd',
       maxDate: "0"
     });
     $( "#datepicker2" ).datepicker({
       changeMonth: true,
       changeYear: true,
       dateFormat: 'yy-mm-dd',
       maxDate: "0"
     });
     $( "#datepicker3" ).datepicker({
       changeMonth: true,
       changeYear: true,
       dateFormat: 'yy-mm-dd',
       maxDate: "0"
     });
     $( "#datepicker4" ).datepicker({
       changeMonth: true,
       changeYear: true,
       dateFormat: 'yy-mm-dd',
       maxDate: "0"
     });
     $( "#datepicker5" ).datepicker({
       changeMonth: true,
       changeYear: true,
       dateFormat: 'yy-mm-dd',
       maxDate: "0"
     });
   } );
      function KpDoneChange(selectObj) {
        debugger;
        var idx = selectObj.selectedIndex;
        var which = selectObj.options[idx].value;
        if (which == '1'){
          document.getElementById("ddlReasonKPWalkout").value = ""
          document.getElementById("ddlReasonKPWalkout").disabled = true;
          document.getElementById("txtNoofKP").disabled = false;
          document.getElementById("txtKPAmt").disabled = false;
        }
        else if (which == '0'){
          document.getElementById("ddlReasonKPWalkout").value = ""
          document.getElementById("ddlReasonKPWalkout").disabled = false;
          document.getElementById("txtNoofKP").disabled = true;
          document.getElementById("txtKPAmt").disabled = true;
        }
        else if (which == ''){
          document.getElementById("ddlReasonKPWalkout").value = ""
          document.getElementById("ddlReasonKPWalkout").disabled = true;
          document.getElementById("txtNoofKP").disabled = true;
          document.getElementById("txtKPAmt").disabled = true;
        }
      }
      function ConvStatusChange(selectObj) {
        debugger;
        var idx = selectObj.selectedIndex;
        var which = selectObj.options[idx].value;
        if (selectObj.name == 'ddlItemConvStatus'){
          element_name="ddlWalkoutReason"
        }
        else if (selectObj.name == 'ddlItemConvStatus1'){
          element_name="ddlWalkoutReason1"
        }
        else if (selectObj.name == 'ddlItemConvStatus2'){
          element_name="ddlWalkoutReason2"
        }
        else if (selectObj.name == 'ddlItemConvStatus3'){
          element_name="ddlWalkoutReason3"
        }
        else if (selectObj.name == 'ddlItemConvStatus4'){
          element_name="ddlWalkoutReason4"
        }
        if (which == 1){
          document.getElementById(element_name).value = ""
          document.getElementById("ddlWalkoutReason").disabled = true;
        }
        else if (which == 0){
          document.getElementById(element_name).value = ""
          document.getElementById(element_name).disabled = false;
        }
      }
      function CategoryChange(selectObj) {
        var itemlist = new Array(10)
        itemlist["--Select--"]=["--Select--"]
        itemlist["Coin"]=["--Select--","CO"]
        itemlist["Diamond Jewellery"]=["--Select--","BA","BL","DH","EG","NL","NP","P1","PD","PE","R1","RG","TM"]
        itemlist["Jadau"]=["--Select--","BA","BL","EG","NL","PD","RG"]
        itemlist["Loose Diamonds"]=["--Select--","LD"]
        itemlist["Plain Gold"]=["--Select--","AC","BA","BL","CH","EC","EG","KA","MP","MS","PD","PS","RG","SC","SE"]
        itemlist["Platinum"]=["--Select--","CH","EG","PD","RG"]
        itemlist["Premium Gold"]=["--Select--","AC","BA","BL","CH","EC","EG","KA","MP","MS","PD","PS","RG","SC","SE"]
        itemlist["Silver"]=["--Select--","Utensils","Coins","Idols","Trays"]
        itemlist["Solitaire Jewellery"]=["--Select--","BA","BL","EG","NL","NP","PD","RG","TM"]
        itemlist["Studded Gold"]=["--Select--","AC","BA","BL","CH","EC","EG","KA","MP","MS","PD","PS","RG","SC","SE"]
        debugger;
        var idx = selectObj.selectedIndex;
        var which = selectObj.options[idx].value;
        if (selectObj.name == 'ddlCategory'){
          element_name="ddlItem"
          budget_lbl="lblBudgetWt1"
          selwt_lbl="lblTotSelWt1"
        }
        else if (selectObj.name == 'ddlCategory1'){
          element_name="ddlItem1"
          budget_lbl="lblBudgetWt2"
          selwt_lbl="lblTotSelWt2"
        }
        else if (selectObj.name == 'ddlCategory2'){
          element_name="ddlItem2"
          budget_lbl="lblBudgetWt3"
          selwt_lbl="lblTotSelWt3"
        }
        else if (selectObj.name == 'ddlCategory3'){
          element_name="ddlItem3"
          budget_lbl="lblBudgetWt4"
          selwt_lbl="lblTotSelWt4"
        }
        else if (selectObj.name == 'ddlCategory4'){
          element_name="ddlItem4"
          budget_lbl="lblBudgetWt5"
          selwt_lbl="lblTotSelWt5"
        }
        if (which == 0){
          document.getElementById(element_name).value = ""
        }
        else{
          if (which=="Diamond Jewellery" || which=="Loose Diamonds"){
            document.getElementById(budget_lbl).textContent="Budget in cts"
            document.getElementById(selwt_lbl).textContent="Selected in cts"
            document.getElementById(budget_lbl).style.padding = "0px 28px 0px 0px"
            document.getElementById(selwt_lbl).style.padding = "0px 8px 0px 0px"
          }
          else if (which=="Coin"||which=="Jadau"||which=="Plain Gold"||which=="Platinum"||which=="Premium Gold"||which=="Silver"||which=="Solitaire Jewellery"||which=="Studded Gold") {
            document.getElementById(budget_lbl).textContent="Budget in gms"
            document.getElementById(selwt_lbl).textContent="Selected in gms"
            document.getElementById(budget_lbl).style.padding = "0px 20px 0px 0px"
            document.getElementById(selwt_lbl).style.padding = "0px 0px 0px 0px"
          }
          else{
            document.getElementById(budget_lbl).textContent="Budget in gms"
            document.getElementById(selwt_lbl).textContent="Selected in gms"
            document.getElementById(budget_lbl).style.padding = "0px 20px 0px 0px"
            document.getElementById(selwt_lbl).style.padding = "0px 0px 0px 0px"
          }
          item_final=itemlist[which]
          var ItemSelect=document.getElementById(element_name);
          var len=ItemSelect.options.length;
          while (ItemSelect.options.length > 0)
          {
            ItemSelect.remove(0);
          }
          var newOption;
          for (var i=0; i<item_final.length; i++) {
          newOption = document.createElement("option");
          newOption.value = item_final[i];
          newOption.text=item_final[i];
          try {
          ItemSelect.add(newOption);
          }
          catch (e) {
          ItemSelect.appendChild(newOption);
          }
         }
        }
        // }
      }
      //]]>
    </script>
  </head>
  <body>
<div class="container" style="font-size:12px">
    <br>
    <span id="lblNote" style="color:Red;">Fields marked with * are mandatory</span>
    <div style="position: relative; float: right;"> 
      <!-- Form no : <b>{{form_no}}</b>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; -->
      <form class="form4" id="form4" action="" method="POST">
      {% csrf_token %}
      <input type="submit" name="logoutbtn" id="logoutbtn" class="btn btn-primary" value="Log out" onclick="this.form4.submit();" style="font-size:12px">
      </form>
      Form no : <b>{{form_no}}</b><br>
      Date : <b>{{today_dt}}</b>
      <br>
      <span id="lblBranchLogin" style="color:SteelBlue;font-weight:bold;">Welcome {{branch_val}}</span>
    </div>
    <br><br><br>
    <ul class="nav nav-tabs">
      <li class="nav-item active">
        <a href="#personal" class="nav-link active" role="tab" data-toggle="tab">Personal Info</a>
      </li>
      <li class="nav-item">
        <a href="#product" class="nav-link" role="tab" data-toggle="tab">Purchase Info</a>
      </li>
      <li class="nav-item">
        <a href="#additional" class="nav-link" role="tab" data-toggle="tab" >Additional Info</a>
      </li>
      <li class="nav-item">
        <a href="#download_rep" class="nav-link" role="tab" data-toggle="tab" >Download Reports</a>
      </li>
      <li class="nav-item">
        <a href="#stat_rep" class="nav-link" role="tab" data-toggle="tab" >Statistic Reports</a>
      </li>
    </ul>
    <div class="tab-content">
      <div class="tab-pane active" id="personal" >
        <form class="form1" id="form1" action="" method="POST">
        {% csrf_token %}
        <div class="one" style="padding-left: 80px;">
          <br>
          <!-- <form class="form2" id="form2" action="" method="POST">
            <input name="txtCustMob" type="text" id="txtCustMob" PlaceHolder="Enter Mobile Number"/>
            <input type="submit" name="findbtn" id="findbtn" class="btn btn-primary" value="Find" onclick="findMob();" style="font-size:12px">
            <button type="button" id="clrbtn4" class="btn btn-primary" onclick="" style="font-size:12px">Clear</button><br>
          </form>
          <br> -->
          
          <div class="two" style="padding-right: 50px;position: relative; float: right; ">
            <span id="lblMailAddr1">Mailing Address</span>
            <textarea name="txtMailAddr1" class="form-control rounded-0" rows="2" id="txtMailAddr1" Placeholder="Flat/Apartment/HouseNo" style="font-size:12px">
            </textarea>

            <!-- <span id="lblMailAddr2">Mailing Address2</span> -->
            <textarea name="txtMailAddr2" class="form-control rounded-0" rows="2" id="txtMailAddr2" Placeholder="Society/Complex/Township" style="font-size:12px">
            </textarea>

            <!-- <span id="Label1">Mailing Address3</span> -->
            <textarea name="txtMailAddr3" class="form-control rounded-0" rows="2" id="txtMailAddr3" Placeholder="Lane/Road/Catchment" style="font-size:12px">
            </textarea>

            <span id="lblCity">City</span>
    <input name="txtCity" type="text" id="txtCity" onblur="validateCity(this);">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

            <span id="lblPin">PinCode</span>
    <input name="txtPin" type="text" maxlength="6" id="txtPin" onblur="validatePin(this);">
            <br />
            <input name="txtSAName" type="text" id="txtSAName" Placeholder="SA NAME" onblur="validateName(this);">
            <input name="txtSAEmpId" type="text" id="txtSAEmpId" Placeholder="EMP ID" >
            <br />

            <input name="txtDeptCRE" type="text" id="txtDeptCRE" Placeholder="Dept CRE" onblur="validateName(this);">
            <input name="txtDeptEmpId" type="text" id="txtDeptEmpId" Placeholder="EMP ID" />
            <br />
            <input name="txtTLName" type="text" id="txtTLName" Placeholder="TL Name" onblur="validateName(this);">
            <input name="txtTLEmpId" type="text" id="txtTLEmpId" Placeholder="EMP ID" >
            <br />
            <span id="lblRefName">Reference Data1</span>
            <input name="txtRefName1" type="text" id="txtRefName1" Placeholder="Name" onblur="validateName(this);">
            <input name="txtRefNumber1" type="text" maxlength="10" id="txtRefNumber1" Placeholder="Contact Number" onblur="validateMobile(this);">
            <br />
            <span id="Label3">Reference Data2</span>
            <input name="txtRefName2" type="text" id="txtRefName2" Placeholder="Name" onblur="validateName(this);">
            <input name="txtRefNumber2" type="text" maxlength="10" id="txtRefNumber2" Placeholder="Contact Number" onblur="validateMobile(this);">
            <br />
            <span id="lblCustHobbies">Customer's Preferences/Hobbies</span>
            <textarea name="txtCustHobbies" class="form-control rounded-0" rows="2" id="txtCustHobbies">
    </textarea>
          </div>

          
            <span id="lblEmail" style="padding-right: 43px;">Email ID</span>
            <input name="txtEmail" type="text" id="txtEmail" style="width:250px" onblur="validateEmail(this);"/><br>
            <span id="lblMobile" style="padding-right: 3px;">Mobile Number</span>
            <input name="txtMobile" type="text" id="txtMobile" style="width:250px" onblur="validateMobile(this);"><b style="color: red">*</b>
            <br />
            <span id="lblCustomerName" style="padding-right: 1px;">Customer Name</span>
            <input name="txtCustName" type="text" id="txtCustName" style="width:250px" onblur="validateName(this);"><b style="color: red">*</b>
            <br />
            <span id="LblNewOldCust" style="padding-right: 13px;">New/<br/>Old Customer</span>
                <select name="ddlNewOldCust" id="ddlNewOldCust" style="width:250px">
    <option value="">--Select--</option>
    <option value="New">New</option>
    <option value="Old">Old</option>

    </select><b style="color: red">*</b>
            <br /><br>
            <span id="lblBrandLoc" style="padding-right: 7px;">Brand Location</span>
            <input name="txtBrandLoc" type="text" id="txtBrandLoc" style="width:250px"/>
            <br />
            <span id="lblOccassion" style="padding-right: 39px;">Occasion</span>
                <select name="ddlOccassion" id="ddlOccassion" style="width:250px">
    <option value="">--Select--</option>
    <option value="Auspicious Buying">Auspicious Buying</option>
    <option value="Casual Buy">Casual Buy</option>
    <option value="Daily Wear">Daily Wear</option>
    <option value="Engagement">Engagement</option>
    <option value="Festival Buying">Festival Buying</option>
    <option value="Gifting">Gifting</option>
    <option value="Investment">Investment</option>
    <option value="Upgradation">Upgradation</option>
    <option value="Wedding">Wedding</option>

    </select>
            <br />
            <span id="lblArea" style="padding-right: 62px;">Area</span>
                <select name="ddlArea" id="ddlArea" style="width:250px">
    <option value="">--Select--</option>
    <option value="0-2.5 Kms">0-2.5 Kms</option>
    <option value="2.5-5.0 Kms">2.5-5.0 Kms</option>
    <option value="5-10 Kms">5-10 Kms</option>
    <option value="10-15 Kms">10-15 Kms</option>
    <option value="15-20 Kms">15-20 Kms</option>
    <option value="20-40 Kms">20-40 Kms</option>
    <option value="40 Kms &amp; beyond">40 Kms &amp; beyond</option>
    <option value="Out of City">Out of City</option>
    <option value="Out of Country">Out of Country</option>
    </select><br>
            <span id="lblCommunity" style="padding-right: 25px;">Community</span>
                <select name="ddlCommunity" id="ddlCommunity" style="width:250px">
    <option value="">--Select--</option>
    <option value="Assamese">Assamese</option>
    <option value="Bengali">Bengali</option>
    <option value="Catholic">Catholic</option>
    <option value="Gujarati">Gujarati</option>
    <option value="Jain">Jain</option>
    <option value="Jaiswal">Jaiswal</option>
    <option value="Maarwadi">Maarwadi</option>
    <option value="Marathi">Marathi</option>
    <option value="Muslim">Muslim</option>
    <option value="Oriya">Oriya</option>
    <option value="Others">Others</option>
    <option value="Punjabi">Punjabi</option>
    <option value="Sindhi">Sindhi</option>
    <option value="S-Indian">S-Indian</option>
    <option value="UP/Bihari">UP/Bihari</option>

    </select><br>

            <span id="lblOccupation" style="padding-right: 26px;">Occupation</span>
                <select name="ddlOccupation" id="ddlOccupation" style="width:250px">
    <option value="">--Select--</option>
    <option value="Builder">Builder</option>
    <option value="Business">Business</option>
    <option value="CA">CA</option>
    <option value="Corporate">Corporate</option>
    <option value="Doctor">Doctor</option>
    <option value="Farmer">Farmer</option>
    <option value="Housewife">Housewife</option>
    <option value="Lawyer">Lawyer</option>
    <option value="NRI">NRI</option>
    <option value="Pensioner">Pensioner</option>
    <option value="Politician">Politician</option>
    <option value="Salaried">Salaried</option>

    </select>
            <br /><br>
            <span id="lblGender" style="padding-right: 48px;">Gender</span>
                <select name="ddlGender" id="ddlGender" style="width:250px">
    <option value="">--Select--</option>
    <option value="Male">Male</option>
    <option value="Female">Female</option>

    </select><b style="color: red">*</b>
            <br />
            <span id="lblAgeGroup"style="padding-right: 29px;">Age Group</span>
                <select name="ddlAgeGroup" id="ddlAgeGroup" style="width:250px">
    <option value="">--Select--</option>
    <option value="Under 18">Under 18</option>
    <option value="18-24">18-24</option>
    <option value="25-35">25-35</option>
    <option value="36-45">36-45</option>
    <option value="46-60">46-60</option>
    <option value="Over 60">Over 60</option>
    </select><b style="color: red">*</b><br>
            <br />
            <span id="lblBirthday" style="padding-right: 43px;">Birthday</span>
                <input name="txtBirthday" type="text" id="datepicker" style="width:250px" onblur="validateDate(this);">
            <br />
            <span id="lblAnnivarsay" style="padding-right: 25px;">Anniversary</span>
                <input name="txtAnniversary" type="text" id="datepicker2"  style="width:250px"/>
        </div><br><br><br><br>
        <div align="center">
          <button type="button" id="savebtn1" class="btn btn-primary" onclick="valEmpty1();" style="font-size:12px">Save and Next</button>
          <button type="button" id="clrbtn1" class="btn btn-primary" style="font-size:12px" onclick="clear_func1();">Clear</button>
        </div>
      </div>
      <div class="tab-pane" id="product" ><br>
        <span id="lblShowConvStatus">Showroom Conversion Status</span>
        <select name="ddlShwConversionStatus" id="ddlShwConversionStatus" style="width:250px">
          <option value="">--Select--</option>
          <option value="1">Yes</option>
          <option value="0">No</option>
        </select>
        <br />
        <span id="lblKP" style="padding-right: 110px;">KP Done</span>
        <select name="ddlKPDone" id="ddlKPDone" style="width:250px" onchange="KpDoneChange(this);">
          <option value="">--Select--</option>
          <option value="1">Yes</option>
          <option value="0">No</option>
        </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <span id="lblNoofKP">No of KP</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&thinsp;
        <input name="txtNoofKP" type="text" id="txtNoofKP" style="width:250px"/>
        <br />
        <span id="lblKPWalkoutReason" style="padding-right: 29px;">Reason for KP Walkout?</span>
        <select name="ddlReasonKPWalkout" id="ddlReasonKPWalkout" style="width:250px">
          <option selected="selected" value="">--Select--</option>
          <option value="Not interested in Installments">Not interested in Installments</option>
          <option value="Will discuss with family">Will discuss with family</option>
          <option value="Competitor has better scheme">Competitor has better scheme</option>
          <option value="Already member of KP scheme">Already member of KP scheme</option>
          <option value="Already member of other Jeweller&#39;s Scheme">Already member of other Jeweller&#39;s Scheme</option>
          <option value="Will think over benefits">Will think over benefits</option>
          <option value="Payment Issues Card/OG/Cash/GV">Payment Issues Card/OG/Cash/GV</option>
          <option value="Cheque/Payment collection at home">Cheque/Payment collection at home</option>
          <option value="NEFT/Online Payment Facility for Installment">NEFT/Online Payment Facility for Installment</option>
          <option value="Bonus Redemption Issue">Bonus Redemption Issue</option>
          <option value="Other issues – for existing KP maturity case only">Other issues – for existing KP maturity case only</option>
          <option value="Want 24kt during Redemption">Want 24kt during Redemption</option>
         </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
         <span id="lblKPAmt">KP Amount</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
         <input name="txtKPAmt" type="text" id="txtKPAmt" style="width:250px"/>
                <hr>
                <span id="lblCategory">Category</span>
                <select name="ddlCategory"  id="ddlCategory" onchange="CategoryChange(this);">
                  <option selected="selected" value="">--Select--</option>
                  <option value="Coin">Coin</option>
                  <option value="Diamond Jewellery">Diamond Jewellery</option>
                  <option value="Jadau">Jadau</option>
                  <option value="Loose Diamonds">Loose Diamonds</option>
                  <option value="Plain Gold">Plain Gold</option>
                  <option value="Platinum">Platinum</option>
                  <option value="Premium Gold">Premium Gold</option>
                  <option value="Silver">Silver</option>
                  <option value="Solitaire Jewellery">Solitaire Jewellery</option>
                  <option value="Studded Gold">Studded Gold</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItem" style="padding-right: 59px;">Item</span>
                <select name="ddlItem" id="ddlItem">
                  <option selected="selected" value="">--Select--</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItemConvStatus" style="padding-right: 23px;">Conv Status</span>
                <select name="ddlItemConvStatus" id="ddlItemConvStatus" class="itemConv" onchange="ConvStatusChange(this);">
                  <option value="">--Select--</option>
                  <option value="1">Yes</option>
                  <option value="0">No</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblWalkoutReason">Walkout Reason</span>
                <select name="ddlWalkoutReason" id="ddlWalkoutReason" disabled="disabled" style="width:150px">
                  <option selected="selected" value="">--Select--</option>
                  <option value="TBZ Gold Rate">TBZ Gold Rate</option>
                  <option value="High Making Charges">High Making Charges</option>
                  <option value="Old gold valuation">Old gold valuation</option>
                  <option value="High Other charges (O.C.)">High Other charges (O.C.)</option>
                  <option value="Diamond pricing high">Diamond pricing high</option>
                  <option value="Overall Selection out of budget">Overall Selection out of budget</option>
                  <option value="Absence of regular stock">Absence of regular stock</option>
                  <option value="Specific Diamond Colour/Clarity (DE /EF)">Specific Diamond Colour/Clarity (DE /EF)</option>
                  <option value="Will visit other tbz store for Variation">Will visit other tbz store for Variation</option>
                  <option value="Want to see more variation in competitor">Want to see more variation in competitor</option>
                  <option value="Will come back with family">Will come back with family</option>
                  <option value="Enquiry regarding product &amp; price for future consideration">Enquiry regarding product &amp; price for future consideration</option>
                  <option value="Promo Offer available in competition">Promo Offer available in competition</option>
                  <option value="Payment Issues Card / OG / Cash / GV">Payment Issues Card / OG / Cash / GV</option>
                  <option value="Advance Paid">Advance Paid</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblReasonDetailed">Reason</span>
                <input name="txtReasonDetailed1" type="text" id="txtReasonDetailed1"/><br>
                <span id="lblBudgetWt1" style="padding-right: 20px;">Budget in gms</span>
                <input name="txtBudInGms1" type="text" id="txtBudInGms1" style="width:75px;" onchange="calc_percent1();" />&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblTotSelWt1">Selected in gms</span>
                <input name="txtTotSelWeight1" id="txtTotSelWeight1" type="text" style="width:75px;" onchange="calc_percent1();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblPerLostGained">%Lost/Gained</span>&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtLostGained1" type="text" id="txtLostGained1" disabled="disabled" style="width:75px;"/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="Label37" style="padding-right: 13px;">Purchase Amt</span>
                <input name="txtPurchaseAmt1" type="text" id="txtPurchaseAmt1" style="width:75px;"/>
                <hr>
                <span id="lblCategory">Category</span>
                <select name="ddlCategory1"  id="ddlCategory1" onchange="CategoryChange(this);">
                  <option selected="selected" value="">--Select--</option>
                  <option value="Coin">Coin</option>
                  <option value="Diamond Jewellery">Diamond Jewellery</option>
                  <option value="Jadau">Jadau</option>
                  <option value="Loose Diamonds">Loose Diamonds</option>
                  <option value="Plain Gold">Plain Gold</option>
                  <option value="Platinum">Platinum</option>
                  <option value="Premium Gold">Premium Gold</option>
                  <option value="Silver">Silver</option>
                  <option value="Solitaire Jewellery">Solitaire Jewellery</option>
                  <option value="Studded Gold">Studded Gold</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItem" style="padding-right: 59px;">Item</span>
                <select name="ddlItem1" id="ddlItem1" >
                  <option selected="selected" value="">--Select--</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItemConvStatus" style="padding-right: 23px;">Conv Status</span>
                <select name="ddlItemConvStatus1" id="ddlItemConvStatus1" class="itemConv" onchange="ConvStatusChange(this);">
                  <option value="">--Select--</option>
                  <option value="1">Yes</option>
                  <option value="0">No</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblWalkoutReason">Walkout Reason</span>
                <select name="ddlWalkoutReason1" id="ddlWalkoutReason1" disabled="disabled" style="width:150px">
                  <option selected="selected" value="">--Select--</option>
                  <option value="TBZ Gold Rate">TBZ Gold Rate</option>
                  <option value="High Making Charges">High Making Charges</option>
                  <option value="Old gold valuation">Old gold valuation</option>
                  <option value="High Other charges (O.C.)">High Other charges (O.C.)</option>
                  <option value="Diamond pricing high">Diamond pricing high</option>
                  <option value="Overall Selection out of budget">Overall Selection out of budget</option>
                  <option value="Absence of regular stock">Absence of regular stock</option>
                  <option value="Specific Diamond Colour/Clarity (DE /EF)">Specific Diamond Colour/Clarity (DE /EF)</option>
                  <option value="Will visit other tbz store for Variation">Will visit other tbz store for Variation</option>
                  <option value="Want to see more variation in competitor">Want to see more variation in competitor</option>
                  <option value="Will come back with family">Will come back with family</option>
                  <option value="Enquiry regarding product &amp; price for future consideration">Enquiry regarding product &amp; price for future consideration</option>
                  <option value="Promo Offer available in competition">Promo Offer available in competition</option>
                  <option value="Payment Issues Card / OG / Cash / GV">Payment Issues Card / OG / Cash / GV</option>
                  <option value="Advance Paid">Advance Paid</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblReasonDetailed">Reason</span>
                <input name="txtReasonDetailed2" type="text" id="txtReasonDetailed2"/><br>
                <span id="lblBudgetWt2" style="padding-right: 20px;">Budget in gms</span>
                <input name="txtBudInGms2" type="text" id="txtBudInGms2" style="width:75px" onchange="calc_percent2();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblTotSelWt2">Selected in gms</span>
                <input name="txtTotSelWeight2" type="text" id="txtTotSelWeight2" style="width:75px" onchange="calc_percent2();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblPerLostGained">%Lost/Gained</span>&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtLostGained2" type="text" id="txtLostGained2" style="width:75px" disabled="disabled" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="Label37" style="padding-right: 13px;">Purchase Amt</span>
                <input name="txtPurchaseAmt2" type="text" id="txtPurchaseAmt2" style="width:75px"/>

                <hr>
                <span id="lblCategory">Category</span>
                <select name="ddlCategory2"  id="ddlCategory2" onchange="CategoryChange(this);">
                  <option selected="selected" value="">--Select--</option>
                  <option value="Coin">Coin</option>
                  <option value="Diamond Jewellery">Diamond Jewellery</option>
                  <option value="Jadau">Jadau</option>
                  <option value="Loose Diamonds">Loose Diamonds</option>
                  <option value="Plain Gold">Plain Gold</option>
                  <option value="Platinum">Platinum</option>
                  <option value="Premium Gold">Premium Gold</option>
                  <option value="Silver">Silver</option>
                  <option value="Solitaire Jewellery">Solitaire Jewellery</option>
                  <option value="Studded Gold">Studded Gold</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItem" style="padding-right: 59px;">Item</span>
                <select name="ddlItem2" id="ddlItem2">
                  <option selected="selected" value="">--Select--</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItemConvStatus" style="padding-right: 23px;">Conv Status</span>
                <select name="ddlItemConvStatus2" id="ddlItemConvStatus2" class="itemConv" onchange="ConvStatusChange(this);">
                  <option value="">--Select--</option>
                  <option value="1">Yes</option>
                  <option value="0">No</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblWalkoutReason">Walkout Reason</span>
                <select name="ddlWalkoutReason2" id="ddlWalkoutReason2" disabled="disabled" style="width:150px">
                  <option selected="selected" value="">--Select--</option>
                  <option value="TBZ Gold Rate">TBZ Gold Rate</option>
                  <option value="High Making Charges">High Making Charges</option>
                  <option value="Old gold valuation">Old gold valuation</option>
                  <option value="High Other charges (O.C.)">High Other charges (O.C.)</option>
                  <option value="Diamond pricing high">Diamond pricing high</option>
                  <option value="Overall Selection out of budget">Overall Selection out of budget</option>
                  <option value="Absence of regular stock">Absence of regular stock</option>
                  <option value="Specific Diamond Colour/Clarity (DE /EF)">Specific Diamond Colour/Clarity (DE /EF)</option>
                  <option value="Will visit other tbz store for Variation">Will visit other tbz store for Variation</option>
                  <option value="Want to see more variation in competitor">Want to see more variation in competitor</option>
                  <option value="Will come back with family">Will come back with family</option>
                  <option value="Enquiry regarding product &amp; price for future consideration">Enquiry regarding product &amp; price for future consideration</option>
                  <option value="Promo Offer available in competition">Promo Offer available in competition</option>
                  <option value="Payment Issues Card / OG / Cash / GV">Payment Issues Card / OG / Cash / GV</option>
                  <option value="Advance Paid">Advance Paid</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblReasonDetailed">Reason</span>
                <input name="txtReasonDetailed3" type="text" id="txtReasonDetailed3"/><br>
                <span id="lblBudgetWt3" style="padding-right: 20px;">Budget in gms</span>
                <input name="txtBudInGms3" type="text" id="txtBudInGms3" style="width:75px" onchange="calc_percent3();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblTotSelWt3">Selected in gms</span>
                <input name="txtTotSelWeight3" id="txtTotSelWeight3" type="text" style="width:75px" onchange="calc_percent3();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblPerLostGained">%Lost/Gained</span>&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtLostGained3" type="text" id="txtLostGained3" style="width:75px" disabled="disabled" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="Label37" style="padding-right: 13px;">Purchase Amt</span>
                <input name="txtPurchaseAmt3" type="text" id="txtPurchaseAmt3" style="width:75px"/>
                <hr>
                <span id="lblCategory">Category</span>
                <select name="ddlCategory3"  id="ddlCategory3" onchange="CategoryChange(this);">
                  <option selected="selected" value="">--Select--</option>
                  <option value="Coin">Coin</option>
                  <option value="Diamond Jewellery">Diamond Jewellery</option>
                  <option value="Jadau">Jadau</option>
                  <option value="Loose Diamonds">Loose Diamonds</option>
                  <option value="Plain Gold">Plain Gold</option>
                  <option value="Platinum">Platinum</option>
                  <option value="Premium Gold">Premium Gold</option>
                  <option value="Silver">Silver</option>
                  <option value="Solitaire Jewellery">Solitaire Jewellery</option>
                  <option value="Studded Gold">Studded Gold</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItem" style="padding-right: 59px;">Item</span>
                <select name="ddlItem3" id="ddlItem3">
                  <option selected="selected" value="">--Select--</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItemConvStatus" style="padding-right: 23px;">Conv Status</span>
                <select name="ddlItemConvStatus3" id="ddlItemConvStatus3" class="itemConv" onchange="ConvStatusChange(this);">
                  <option value="">--Select--</option>
                  <option value="1">Yes</option>
                  <option value="0">No</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblWalkoutReason">Walkout Reason</span>
                <select name="ddlWalkoutReason3" id="ddlWalkoutReason3" disabled="disabled" style="width:150px">
                  <option selected="selected" value="">--Select--</option>
                  <option value="TBZ Gold Rate">TBZ Gold Rate</option>
                  <option value="High Making Charges">High Making Charges</option>
                  <option value="Old gold valuation">Old gold valuation</option>
                  <option value="High Other charges (O.C.)">High Other charges (O.C.)</option>
                  <option value="Diamond pricing high">Diamond pricing high</option>
                  <option value="Overall Selection out of budget">Overall Selection out of budget</option>
                  <option value="Absence of regular stock">Absence of regular stock</option>
                  <option value="Specific Diamond Colour/Clarity (DE /EF)">Specific Diamond Colour/Clarity (DE /EF)</option>
                  <option value="Will visit other tbz store for Variation">Will visit other tbz store for Variation</option>
                  <option value="Want to see more variation in competitor">Want to see more variation in competitor</option>
                  <option value="Will come back with family">Will come back with family</option>
                  <option value="Enquiry regarding product &amp; price for future consideration">Enquiry regarding product &amp; price for future consideration</option>
                  <option value="Promo Offer available in competition">Promo Offer available in competition</option>
                  <option value="Payment Issues Card / OG / Cash / GV">Payment Issues Card / OG / Cash / GV</option>
                  <option value="Advance Paid">Advance Paid</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblReasonDetailed">Reason</span>
                <input name="txtReasonDetailed4" type="text" id="txtReasonDetailed4" /><br>
                <span id="lblBudgetWt4" style="padding-right: 20px;">Budget in gms</span>
                <input name="txtBudInGms4" type="text" id="txtBudInGms4" style="width:75px" onchange="calc_percent4();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblTotSelWt4">Selected in gms</span>
                <input name="txtTotSelWeight4" id="txtTotSelWeight4" type="text" style="width:75px" onchange="calc_percent4();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblPerLostGained">%Lost/Gained</span>&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtLostGained4" type="text" id="txtLostGained4" style="width:75px" disabled="disabled" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="Label37" style="padding-right: 13px;">Purchase Amt</span>
                <input name="txtPurchaseAmt4" type="text" id="txtPurchaseAmt4" style="width:75px"/>
                <hr>
                <span id="lblCategory">Category</span>
                <select name="ddlCategory4"  id="ddlCategory4" onchange="CategoryChange(this);">
                  <option selected="selected" value="">--Select--</option>
                  <option value="Coin">Coin</option>
                  <option value="Diamond Jewellery">Diamond Jewellery</option>
                  <option value="Jadau">Jadau</option>
                  <option value="Loose Diamonds">Loose Diamonds</option>
                  <option value="Plain Gold">Plain Gold</option>
                  <option value="Platinum">Platinum</option>
                  <option value="Premium Gold">Premium Gold</option>
                  <option value="Silver">Silver</option>
                  <option value="Solitaire Jewellery">Solitaire Jewellery</option>
                  <option value="Studded Gold">Studded Gold</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItem" style="padding-right: 59px;">Item</span>
                <select name="ddlItem4" id="ddlItem4">
                  <option selected="selected" value="">--Select--</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblItemConvStatus" style="padding-right: 23px;">Conv Status</span>
                <select name="ddlItemConvStatus4" id="ddlItemConvStatus4" class="itemConv" onchange="ConvStatusChange(this);">
                  <option value="">--Select--</option>
                  <option value="1">Yes</option>
                  <option value="0">No</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblWalkoutReason">Walkout Reason</span>
                <select name="ddlWalkoutReason4" id="ddlWalkoutReason4" disabled="disabled" style="width:150px">
                  <option selected="selected" value="">--Select--</option>
                  <option value="TBZ Gold Rate">TBZ Gold Rate</option>
                  <option value="High Making Charges">High Making Charges</option>
                  <option value="Old gold valuation">Old gold valuation</option>
                  <option value="High Other charges (O.C.)">High Other charges (O.C.)</option>
                  <option value="Diamond pricing high">Diamond pricing high</option>
                  <option value="Overall Selection out of budget">Overall Selection out of budget</option>
                  <option value="Absence of regular stock">Absence of regular stock</option>
                  <option value="Specific Diamond Colour/Clarity (DE /EF)">Specific Diamond Colour/Clarity (DE /EF)</option>
                  <option value="Will visit other tbz store for Variation">Will visit other tbz store for Variation</option>
                  <option value="Want to see more variation in competitor">Want to see more variation in competitor</option>
                  <option value="Will come back with family">Will come back with family</option>
                  <option value="Enquiry regarding product &amp; price for future consideration">Enquiry regarding product &amp; price for future consideration</option>
                  <option value="Promo Offer available in competition">Promo Offer available in competition</option>
                  <option value="Payment Issues Card / OG / Cash / GV">Payment Issues Card / OG / Cash / GV</option>
                  <option value="Advance Paid">Advance Paid</option>
                </select>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="lblReasonDetailed">Reason</span>
                <input name="txtReasonDetailed5" type="text" id="txtReasonDetailed5"/><br>
                <span id="lblBudgetWt5" style="padding-right: 20px;">Budget in gms</span>
                <input name="txtBudInGms5" type="text" id="txtBudInGms5" style="width:75px" onchange="calc_percent5();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblTotSelWt5">Selected in gms</span>
                <input name="txtTotSelWeight5" id="txtTotSelWeight5" type="text" style="width:75px" onchange="calc_percent5();"/>&nbsp;&thinsp;&nbsp;&nbsp;&nbsp;
                <span id="lblPerLostGained">%Lost/Gained</span>&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtLostGained5" type="text" id="txtLostGained5" style="width:75px" disabled="disabled" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span id="Label37" style="padding-right: 13px;">Purchase Amt</span>
                <input name="txtPurchaseAmt5" type="text" id="txtPurchaseAmt5" style="width:75px"/>
                <div align="center">
                  <br>
                  <button type="button" id="savebtn2" class="btn btn-primary" onclick="valEmpty2();" style="font-size:12px">Save and Next</button>
                  <button type="button" id="clrbtn2" class="btn btn-primary" onclick="clear_func2();" style="font-size:12px">Clear</button>
                </div>
      </div>
      <div class="tab-pane" id="additional"><br>
        <span id="lblTbz">How did you got to know about TBZ?</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <select name="ddlTBZ" id="ddlTBZ" style="width:250px">
              <option value="">--Select--</option>
              <option value="Newspapers?">Newspapers?</option>
              <option value="TV">TV</option>
              <option value="Radio">Radio</option>
              <option value="Hoardings">Hoardings</option>
              <option value="Referred by friends / relatives">Referred by friends / relatives</option>
              <option value="Internet / Social Media">Internet / Social Media</option>
            </select><b style="color: red">*</b><br>
        <span id="lblDetails">Details</span>
        <textarea name="txtDetails" class="form-control rounded-0" rows="2" style="width:545px" id="txtDetails">
        </textarea>
        <span id="lblPreferredJeweller">Most Preferred<br> Jeweller</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

           <input name="txtPrefJeweller" type="text" id="txtPrefJeweller" style="width:250px"/><br />

        <span id="lblMOFC">Mode of Future<br> Communication</span>&nbsp;&nbsp;&nbsp;
        <select name="ddlMOFC" id="ddlMOFC" style="width:250px">
          <option value="">--Select--</option>
          <option value="Calls">Calls</option>
          <option value="SMS">SMS</option>
          <option value="Emails">Emails</option>
          <option value="Others">Others</option>
        </select>
        <input name="txtOtherSpecify" type="text" id="txtOtherSpecify" Placeholder="Others Specify" style="display:none" />
        <div align="center">
          <br>
          <input type="submit" name="submitbtn" id="submitbtn" class="btn btn-primary" value="Submit" style="font-size:12px" onclick="valEmpty3();">
          <button type="button" id="clrbtn3" class="btn btn-primary" onclick="clear_func3();" style="font-size:12px">Clear</button>
        </div>
      </form>
      </div>
      <div class="tab-pane" id="download_rep" >
        <form class="form3" id="form3" action="/EntryDetails/" method="POST">
          {% csrf_token %}
            <br>
            <span id="lbldwnldmethod">Search by</span>&nbsp;&nbsp;&nbsp;
            <select name="ddldwnldmethod" id="ddldwnldmethod" onchange="downmethchange(this);">
              <option value="">--Select--</option>
              <option value="Date">Date</option>
              <option value="Mobile Number">Mobile Number</option>
            </select>
            <br><br>
            <span id="lblfromdate">From Date</span>&nbsp;
            <input name="txtfromdate" type="text" id="datepicker3">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <span id="lbltodate">To Date</span>&nbsp;
            <input name="txttodate" type="text" id="datepicker4">
            <br><br>
            <span id="lblfiltermob">Mobile No</span>
            <input name="txtfiltermob" type="text" id="txtfiltermob" onblur="validateMobile(this);">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <div align="center">
              <input type="submit" name="btndwnldrep" id="btndwnldrep" value="Submit" class="btn btn-primary" onclick="download_rep();" style="font-size:12px">
            </div>
        </form>        
      </div>
      <div class="tab-pane" id="stat_rep" >
        <form class="form4" id="form4" action="" method="POST">
        {% csrf_token %}
          <br>
          <!-- <span id="lblchartdate">Enter Date</span>
          <input name="txtchartdate" type="text" id="datepicker5"> -->
          <!-- <select name="ddlchartmethod" id="ddlchartmethod" style="width:250px">
            <option value="">--Select--</option>
            <option value="Date Range">Filter by Date Range</option>
            <option value="Full data">Full data</option>
          </select> -->
          <input type="submit" name="btnshowchart" id="btnshowchart" value="Show Chart" class="btn btn-primary" onclick="this.form4.submit();" style="font-size:12px">
        </form>
      </div>
    </div>
</div>
</body>
</html>



login.html

<!DOCTYPE html>
<html>
<head>
  <title></title>
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/javascript.util/0.12.12/javascript.util.min.js"></script>
  <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
  <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
  <script type="text/javascript">
    $(document).ready(function() {
        window.history.pushState(null, "", window.location.href);        
        window.onpopstate = function() {
            window.history.pushState(null, "", window.location.href);
        };
    });
  </script>
</head>
<body>
<div class="container" style="font-size:12px">
  <div class="jumbotron" align="center">
    {% if messages %}
    <ul class="messages">
        {% for message in messages %}
        <li{% if message.tags %} class="{{ message.tags }}"{% endif %}><span style="color:red">{{ message }}</span></li>
        {% endfor %}
    </ul>
    {% endif %}
    <form method="post" action="/EntryDetails/" id="loginform">
        {% csrf_token %}
        <span id="lblUsername">Username</span>
        <input name="txtUsername" type="text" id="txtUsername" placeHolder="Enter Username" /><br>
        <span id="lblPwd">Password</span>&nbsp;
        <input name="txtPwd" type="password" id="txtPwd" placeholder="Enter Password"/><br><br>
        <input type="submit" name="btnlogin" id="btnlogin" class="btn btn-primary" value="Login" onclick="this.loginform.submit();" style="font-size:12px">
        <a href="/ChangePassword/">Change Password</a>
    </form>
  </div>
</div>
</body>
</html>




new.html

<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet" id="bootstrap-css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script src="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/javascript.util/0.12.12/javascript.util.min.js"></script>
    <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
    <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
    <script type="text/javascript">
    $(document).ready(function() {
        window.history.pushState(null, "", window.location.href);        
        window.onpopstate = function() {
            window.history.pushState(null, "", window.location.href);
        };
    });
    function download_csv(csv, filename) {
        var csvFile;
        var downloadLink;

        // CSV FILE
        csvFile = new Blob([csv], {type: "text/csv"});

        // Download link
        downloadLink = document.createElement("a");

        // File name
        downloadLink.download = filename;

        // We have to create a link to the file
        downloadLink.href = window.URL.createObjectURL(csvFile);

        // Make sure that the link is not displayed
        downloadLink.style.display = "none";

        // Add the link to your DOM
        document.body.appendChild(downloadLink);

        // Lanzamos
        downloadLink.click();
    }

    function export_table_to_csv(html, filename) {
    	var csv = [];
    	var rows = document.querySelectorAll("table tr");

        for (var i = 0; i < rows.length; i++) {
    		var row = [], cols = rows[i].querySelectorAll("td, th");

            for (var j = 0; j < cols.length; j++)
                row.push(cols[j].innerText);

    		csv.push(row.join(","));
    	}

        // Download CSV
        download_csv(csv.join("\n"), filename);
    }

    function get_csv_file() {
      var html = document.querySelector("table").outerHTML;
    	export_table_to_csv(html, "table.csv");
    }
    </script>
  </head>
  <body style="font-size:12px">
    <form class="loginform" id="loginform" action="/EntryDetails/" method="POST">
        {% csrf_token %}
        <table id="myTable" border="1px">
          <tr>
            <th>FormNo</th>
            <th>UserId</th>
            <th>BranchName</th>
            <th>MobileNumber</th>
            <th>CustomerName</th>
            <th>NewOldCustomer</th>
            <th>BrandLocation</th>
            <th>Community</th>
            <th>Occupation</th>
            <th>Gender</th>
            <th>Age</th>
            <th>Birthday</th>
            <th>Anniversary</th>
            <th>Occassion</th>
            <th>Area</th>
            <th>EmailId</th>
            <th>KP_Amount</th>
            <th>ShowroomConvSta</th>
            <th>KP_Done</th>
            <th>Address1</th>
            <th>Address2</th>
            <th>Address3</th>
            <th>City</th>
            <th>Pincode</th>
            <th>Date</th>
            <th>SAName</th>
            <th>SAEmpId</th>
            <th>DeptCRE</th>
            <th>DeptEmpId</th>
            <th>TLName</th>
            <th>TLEmpId</th>
            <th>RefData1</th>
            <th>RefNumber1</th>
            <th>RefData2</th>
            <th>RefNumber2</th>
            <th>NoOfKP</th>
            <th>KPWalkoutReason</th>
            <th>PreferredJew</th>
            <th>Hobbies</th>
            <th>KnowTBZ</th>
            <th>Details</th>
            <th>ModeOfComm</th>
            <th>OtherMOCReason</th>
          </tr>
          <tr>
            {%for a in db_tuple%}
              {%for b in a%}
                <td>{{b}}</td>
                {% if forloop.counter|divisibleby:"43" %}
                  </tr>
                {%endif%}
              {%endfor%}
          {%endfor%}
        </table>
        <div align="center">
            <input type="button" id="downloadbtn" class="btn btn-primary" value="Download CSV" onclick="get_csv_file();" style="font-size:12px">
            <input type="submit" name="backbtn" id="backbtn" class="btn btn-primary" value="Back" onclick="this.loginform.submit();" style="font-size:12px">
        </div>
    </form>
  </body>
</html>


register.html

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head><title>
</title><link rel="stylesheet" href="http://code.jquery.com/ui/1.10.3/themes/smoothness/jquery-ui.css" />
    <script src="http://code.jquery.com/jquery-1.9.1.js"></script>
    <script src="http://code.jquery.com/ui/1.10.3/jquery-ui.js"></script>
    <script type="text/javascript">
        function numeric_check(val) {
            var e = event || evt; // for trans-browser compatibility
            var charCode = e.which || e.keyCode;

            // for only numeric and dot allowed
            if (!(charCode >= 48 && charCode <= 57)) {
                alert("Only numbers allowed");
                return false;
            }
            return true;
        }
        $('#btnSubmit').click(function () {
            alert("Inside button click");
        });
        function valEmpty() {

            //var branchName = $('#txtBranchName').val();
            //alert(branchName);
            //var txtUId = $('#txtUId').val();

            //var txtPwd = $('#txtPwd').val();

            if ($('#txtBranchName').val() == "") {
                alert("Branch Name cannot be blank");
                return false;
            }
            else if ($('#txtUId').val() == "") {
                alert("UserId cannot be blank");
                return false;
            }
            else if ($('#txtPwd').val() == "") {
                alert("Password cannot be blank");
                return false;
            }
            else {
                return form1.submit();
            }

        }
    </script>
</head>
<body>
    <form method="post" id="form1">
      {% csrf_token %}
        <div align="center">

            <br />
            <h3>REGISTER</h3>

        </div>
        <div>
            <span id="lblNote" style="color:Red;">Fields marked with * are mandatory</span>
        </div>
        <div style="margin-right: 20%">
            <div id="pnlRegister" style="border-color:#990000;border-width:1px;border-style:Solid;width:40%;">

                <br />
                <span id="lblBranchName">Branch Name</span>&nbsp;&nbsp;&nbsp;
                <input name="txtBranchName" type="text" id="txtBranchName" /><b style="color: red">*</b>
                <br />
                <br />
                <span id="lblUId">UserId</span>&nbsp;&nbsp;&nbsp;
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtUId" type="text" id="txtUId" /><b style="color: red">*</b>
                <br />
                <br />
                <span id="lblPwd">Password</span>&nbsp;&nbsp;&nbsp;
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtPwd" type="password" id="txtPwd" /><b style="color: red">*</b>
                <br />
                <br />
                <span id="lblNo">MobileNo/<br/>PhoneNo</span>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <input name="txtNumber" type="text" id="txtNumber" onKeyPress="return numeric_check(this.value);" />
                <br />
                <br />
                <span id="lblAddress">Address</span>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <textarea name="txtAddr" rows="2" cols="20" id="txtAddr">
</textarea>

</div>
            <br />
            <input type="submit" name="btnSubmit" value="SUBMIT" onclick="javascript:return valEmpty();" id="btnSubmit" style="text-align: center" /><br />

            <input type="submit" name="btnLogOut" value="LOGOUT" id="btnLogOut" style="position: absolute; right: 0; top: 0" />
        </div>
    </form>
</body>
</html>



MYSQL BACKUP


'branch', 'CREATE TABLE `branch` (\n  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,\n  `Password` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BranchName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `PhoneNumber` varchar(15) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Address` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `py_passwd` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  PRIMARY KEY (`UserId`)\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'category', 'CREATE TABLE `category` (\n  `ID` int(11) DEFAULT NULL,\n  `Name` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'categorydetails', 'CREATE TABLE `categorydetails` (\n  `FormNo` int(11) NOT NULL,\n  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,\n  `Category1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Item1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ItemConvStatus1` tinyint(1) DEFAULT NULL,\n  `WalkOutReason1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Reason` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BudgetInGms` decimal(18,3) DEFAULT NULL,\n  `TotalWeight` decimal(18,3) DEFAULT NULL,\n  `LostGained` decimal(18,3) DEFAULT NULL,\n  `PurchaseAmt` decimal(18,3) DEFAULT NULL,\n  `Category2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Item2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ItemConvStatus2` tinyint(1) DEFAULT NULL,\n  `WalkOutReason2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Reason2` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BudgetInGms2` decimal(18,3) DEFAULT NULL,\n  `TotalWeight2` decimal(18,3) DEFAULT NULL,\n  `LostGained2` decimal(18,3) DEFAULT NULL,\n  `PurchaseAmt2` decimal(18,3) DEFAULT NULL,\n  `Category3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Item3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ItemConvStatus3` tinyint(1) DEFAULT NULL,\n  `WalkOutReason3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Reason3` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BudgetInGms3` decimal(18,3) DEFAULT NULL,\n  `TotalWeight3` decimal(18,3) DEFAULT NULL,\n  `LostGained3` decimal(18,3) DEFAULT NULL,\n  `PurchaseAmt3` decimal(18,3) DEFAULT NULL,\n  `Category4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Item4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ItemConvStatus4` tinyint(1) DEFAULT NULL,\n  `WalkOutReason4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Reason4` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BudgetInGms4` decimal(18,3) DEFAULT NULL,\n  `TotalWeight4` decimal(18,3) DEFAULT NULL,\n  `LostGained4` decimal(18,3) DEFAULT NULL,\n  `PurchaseAmt4` decimal(18,3) DEFAULT NULL,\n  `Category5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Item5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ItemConvStatus5` tinyint(1) DEFAULT NULL,\n  `WalkOutReason5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Reason5` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BudgetInGms5` decimal(18,3) DEFAULT NULL,\n  `TotalWeight5` decimal(18,3) DEFAULT NULL,\n  `LostGained5` decimal(18,3) DEFAULT NULL,\n  `PurchaseAmt5` decimal(18,3) DEFAULT NULL,\n  PRIMARY KEY (`FormNo`)\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'community', 'CREATE TABLE `community` (\n  `ID` int(11) DEFAULT NULL,\n  `Name` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'entrydetails', 'CREATE TABLE `entrydetails` (\n  `FormNo` int(11) NOT NULL,\n  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,\n  `BranchName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `MobileNumber` varchar(10) CHARACTER SET utf8mb4 NOT NULL,\n  `CustomerName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `NewOldCustomer` varchar(5) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `BrandLocation` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Community` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Occupation` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Gender` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Age` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Birthday` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Anniversary` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Occassion` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Area` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `EmailId` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `KP_Amount` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ShowroomConvSta` tinyint(1) DEFAULT NULL,\n  `KP_Done` tinyint(1) DEFAULT NULL,\n  `Address1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Address2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Address3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `City` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Pincode` varchar(6) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Date` date DEFAULT NULL,\n  `SAName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `SAEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `DeptCRE` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `DeptEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `TLName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `TLEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `RefData1` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `RefNumber1` varchar(12) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `RefData2` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `RefNumber2` varchar(12) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `NoOfKP` varchar(500) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `KPWalkoutReason` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `PreferredJew` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Hobbies` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `KnowTBZ` varchar(150) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `Details` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,\n  `ModeOfComm` varchar(10) DEFAULT NULL,\n  `OtherMOCReason` varchar(150) CHARACTER SET utf8mb4 DEFAULT NULL,\n  PRIMARY KEY (`FormNo`)\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'item', 'CREATE TABLE `item` (\n  `Id` int(11) DEFAULT NULL,\n  `Name` varchar(20) DEFAULT NULL\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'occupation', 'CREATE TABLE `occupation` (\n  `ID` int(11) DEFAULT NULL,\n  `Name` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL\n) ENGINE=InnoDB DEFAULT CHARSET=utf8'
'insertCatDetails', 'STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION', 'CREATE DEFINER=`root`@`%` PROCEDURE `insertCatDetails`(\n  p_formNo int,\n  p_uid nvarchar(50),\n  p_cat1 nvarchar(50)/* =null */,\n  p_item1 nvarchar(50)/* =null */,\n  p_itemConvSta1 tinyint/* =0 */,\n  p_walkoutReason1 nvarchar(50)/* =null */,\n  p_reason nvarchar(300)/* =null */,\n  p_budginInGms decimal(18,3)/* =0 */,\n  p_totalWt decimal(18,3)/* =0 */,\n  p_lostgained decimal(18,3)/* =0 */,\n  p_purchaseAmt decimal(18,3)/* =0 */,\n  p_cat2 nvarchar(50)/* =null */,\n  p_item2 nvarchar(50)/* =null */,\n  p_itemConvSta2 tinyint/* =0 */,\n  p_walkoutReason2 nvarchar(50)/* =null */,\n  p_reason2 nvarchar(300)/* =null */,\n  p_budginInGms2 decimal(18,3)/* =0 */,\n  p_totalWt2 decimal(18,3)/* =0 */,\n  p_lostgained2 decimal(18,3)/* =0 */,\n  p_purchaseAmt2 decimal(18,3)/* =0 */,\n  p_cat3 nvarchar(50)/* =null */,\n  p_item3 nvarchar(50)/* =null */,\n  p_itemConvSta3 tinyint/* =0 */,\n  p_walkoutReason3 nvarchar(50)/* =null */,\n  p_reason3 nvarchar(300)/* =null */,\n  p_budginInGms3 decimal(18,3)/* =0 */,\n  p_totalWt3 decimal(18,3)/* =0 */,\n  p_lostgained3 decimal(18,3)/* =0 */,\n  p_purchaseAmt3 decimal(18,3)/* =0 */,\n  p_cat4 nvarchar(50)/* =null */,\n  p_item4 nvarchar(50)/* =null */,\n  p_itemConvSta4 tinyint/* =0 */,\n  p_walkoutReason4 nvarchar(50)/* =null */,\n  p_reason4 nvarchar(300)/* =null */,\n  p_budginInGms4 decimal(18,3)/* =0 */,\n  p_totalWt4 decimal(18,3)/* =0 */,\n  p_lostgained4 decimal(18,3)/* =0 */,\n  p_purchaseAmt4 decimal(18,3)/* =0 */,\n  p_cat5 nvarchar(50)/* =null */,\n  p_item5 nvarchar(50)/* =null */,\n  p_itemConvSta5 tinyint/* =0 */,\n  p_walkoutReason5 nvarchar(50)/* =null */,\n  p_reason5 nvarchar(300)/* =null */,\n  p_budginInGms5 decimal(18,3)/* =0 */,\n  p_totalWt5 decimal(18,3)/* =0 */,\n  p_lostgained5 decimal(18,3)/* =0 */,\n  p_purchaseAmt5 decimal(18,3)/* =0 */\n\n)\nBEGIN\n       INSERT INTO CATEGORYDETAILS\n	   (\n	        FormNo,\n	        UserId\n			,Category1\n			,Item1\n			,ItemConvStatus1\n			,WalkOutReason1\n			,Reason\n			,BudgetInGms\n			,TotalWeight\n			,LostGained\n			,PurchaseAmt\n			,Category2\n			,Item2\n			,ItemConvStatus2\n			,WalkOutReason2\n			,Reason2\n			,BudgetInGms2\n			,TotalWeight2\n			,LostGained2\n			,PurchaseAmt2\n			,Category3\n			,Item3\n			,ItemConvStatus3\n			,WalkOutReason3\n			,Reason3\n			,BudgetInGms3\n			,TotalWeight3\n			,LostGained3\n			,PurchaseAmt3\n			,Category4\n			,Item4\n			,ItemConvStatus4\n			,WalkOutReason4\n			,Reason4\n			,BudgetInGms4\n			,TotalWeight4\n			,LostGained4\n			,PurchaseAmt4\n			,Category5\n			,Item5\n			,ItemConvStatus5\n			,WalkOutReason5\n			,Reason5\n			,BudgetInGms5\n			,TotalWeight5\n			,LostGained5\n			,PurchaseAmt5\n\n\n\n	   )\n	   VALUES\n	   (\n	          p_formNo,\n	          p_uid ,\n			  p_cat1 ,\n			  p_item1 ,\n			  p_itemConvSta1 ,\n			  p_walkoutReason1 ,\n			  p_reason ,\n			  p_budginInGms ,\n			  p_totalWt ,\n			  p_lostgained ,\n			  p_purchaseAmt ,\n			  p_cat2 ,\n			  p_item2 ,\n			  p_itemConvSta2 ,\n			  p_walkoutReason2 ,\n			  p_reason2 ,\n			  p_budginInGms2 ,\n			  p_totalWt2 ,\n			  p_lostgained2 ,\n			  p_purchaseAmt2 ,\n			  p_cat3 ,\n			  p_item3 ,\n			  p_itemConvSta3 ,\n			  p_walkoutReason3 ,\n			  p_reason3 ,\n			  p_budginInGms3 ,\n			  p_totalWt3 ,\n			  p_lostgained3 ,\n			  p_purchaseAmt3 ,\n			  p_cat4 ,\n			  p_item4 ,\n			  p_itemConvSta4 ,\n			  p_walkoutReason4 ,\n			  p_reason4 ,\n			  p_budginInGms4 ,\n			  p_totalWt4 ,\n			  p_lostgained4 ,\n			  p_purchaseAmt4 ,\n			  p_cat5 ,\n			  p_item5 ,\n			  p_itemConvSta5 ,\n			  p_walkoutReason5 ,\n			  p_reason5 ,\n			  p_budginInGms5 ,\n			  p_totalWt5 ,\n			  p_lostgained5 ,\n			  p_purchaseAmt5 \n	   );\n\nEND', 'utf8', 'utf8_general_ci', 'utf8_general_ci'
'insertEntryDetails', 'STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION', 'CREATE DEFINER=`root`@`%` PROCEDURE `insertEntryDetails`(\n  p_formNo int,\n  p_uid nvarchar(50),\n  p_branchName nvarchar(50),\n  p_mobileNo nvarchar(10),\n  p_custName nvarchar(50),\n  p_newoldCust nvarchar(5),\n  p_brandLocation nvarchar(50)/* =null */,\n  p_community nvarchar(50)/* =null */,\n  p_occupation nvarchar(50)/* =null */,\n  p_gender nvarchar(10),\n  p_age nvarchar(100),\n  p_birthday nvarchar(50)/* =null */,\n  p_anniversary nvarchar(200)/* =null */,\n  p_occassion nvarchar(50)/* =null */,\n  p_area nvarchar(50)/* =null */,\n  p_email nvarchar(50)/* =null */,\n  p_kpAmount nvarchar(50)/* =null */,\n  p_showconvsta tinyint/* =0 */,\n  p_kpdone tinyint/* =0 */,\n  p_address1 nvarchar(50)/* =null */,\n  p_address2 nvarchar(50)/* =null */,\n  p_address3 nvarchar(50)/* =null */,\n  p_city nvarchar(50)/* =null */,\n  p_pincode nvarchar(6)/* =null */,\n  p_enterdate date/* =null */,\n  p_saname nvarchar(50)/* =null */,\n  p_saempid nvarchar(10)/* =null */,\n  p_deptcre nvarchar(50)/* =null */,\n  p_deptempid nvarchar(10)/* =null */,\n  p_tlname nvarchar(50)/* =null */,\n  p_tlempid nvarchar(10)/* =null */,\n  p_refData1 nvarchar(200)/* =null */,\n  p_refNumber1 nvarchar(12)/* =null */,\n  p_refData2 nvarchar(200)/* =null */,\n  p_refNumber2 nvarchar(12)/* =null */,\n  p_noofkp nvarchar(500)/* =null */,\n  p_kpwalkoutreason nvarchar(200)/* =null */,\n  p_prefJew nvarchar(50)/* =null */,\n  p_hobbies nvarchar(100)/* =null */,\n  p_knowTbz nvarchar(150)/* =null */,\n  p_details nvarchar(200)/* =null */,\n  p_mofc varchar(10)/* =null */,\n  p_otherMOCReason nvarchar(150)/* =null */\n\n)\nBEGIN\n		INSERT INTO EntryDetails(\n		FormNo,\n		UserId,\n		BranchName,\n		MobileNumber,\n		CustomerName,\n		NewOldCustomer,\n		BrandLocation,\n		Community,\n		Occupation,\n		Gender,\n		Age,\n		Birthday,\n		Anniversary,\n		Occassion,\n		Area,\n		EmailId,\n		KP_Amount,\n		ShowroomConvSta,\n		KP_Done,\n		Address1,\n		Address2,\n		Address3,\n		City,\n		Pincode,\n		Date,\n		SAName,\n		SAEmpId,\n		DeptCRE,\n		DeptEmpId,\n		TLName,\n		TLEmpId,\n		RefData1,\n		RefNumber1,\n		RefData2,\n		RefNumber2,\n		NoOfKP,\n		KPWalkoutReason,\n		PreferredJew,\n		Hobbies,\n		KnowTBZ,\n		Details,\n		ModeOfComm,\n		OtherMOCReason\n		)\n		VALUES\n		(\n		  p_formNo,\n		  p_uid,\n		  p_branchName ,\n		  p_mobileNo ,\n		  p_custName ,\n		  p_newoldCust ,\n		  p_brandLocation ,\n		  p_community ,\n		  p_occupation,\n		  p_gender ,\n		  p_age ,\n		  p_birthday ,\n		  p_anniversary ,\n		  p_occassion ,\n		  p_area ,\n		  p_email ,\n		  p_kpAmount ,\n		  p_showconvsta ,\n		  p_kpdone ,\n		  p_address1 ,\n		  p_address2 ,\n		  p_address3 ,\n		  p_city ,\n		  p_pincode ,\n		  p_enterdate ,\n		  p_saname ,\n		  p_saempid ,\n		  p_deptcre ,\n		  p_deptempid ,\n		  p_tlname ,\n		  p_tlempid,\n		  p_refData1 ,\n		  p_refNumber1 ,\n		  p_refData2 ,\n		  p_refNumber2 ,\n		  p_noofkp ,\n		  p_kpwalkoutreason ,\n		  p_prefJew ,\n		  p_hobbies ,\n		  p_knowTbz,\n		  p_details,\n          p_mofc,\n		  p_otherMOCReason\n		);\nEND', 'utf8', 'utf8_general_ci', 'utf8_general_ci'





details_branch

-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `branch`
--

DROP TABLE IF EXISTS `branch`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `branch` (
  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,
  `Password` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BranchName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `PhoneNumber` varchar(15) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Address` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `py_passwd` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  PRIMARY KEY (`UserId`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `branch`
--

LOCK TABLES `branch` WRITE;
/*!40000 ALTER TABLE `branch` DISABLE KEYS */;
INSERT INTO `branch` VALUES ('Ahm','iB/AgeqzGBZ22WsePJON9Q==','Ahmedabad','','','tbz1234*'),('All','wS+AQevIClCKhDDl4G+9kg==','All',NULL,NULL,NULL),('Bandra','I38IhIgQIxr0PWu/s2aepA==','Bandra','','',NULL),('Basheerbagh','vWMxDewbNwujOCs4jTDwkw==','Basheerbagh','','',NULL),('Bhavnagar','HJAFw1vfh+8x5b5aYKVYcw==','Bhavnagar','','',NULL),('Bhopal','jb8efyc9e0R7cNXz/txLJQ==','Bhopal','','',NULL),('Borivali','8AGfFFViVdXLG4yV0FUEXQ==','Borivali','','',NULL),('Camac','SquDGUcRmUv00ld9bD0G2w==','Kolkata-Camac Street','','',NULL),('Churchgate','lcMaG4uYHhMFK3vup37wAw==','Churchgate','','',NULL),('CIT','/1G5weZp01p1np+8sXxtcQ==','Kolkata-CIT Road','','',NULL),('Cochin','wrC7762dAHv1NqD98EWclQ==','Cochin','','',NULL),('Dhanbad','nD6ztfpynQzMtJkQJXQflQ==','Dhanbad','','',NULL),('Gandhidham','a5Rb/xytaZ6wjC3WUxoExQ==','Gandhidham','','',NULL),('Ghatkoper','3RrYvuhZMu7AKyRqlcYZ8w==','Ghatkoper','','',NULL),('Indore','msIZ8gpk1YcT9uXCrNGsWA==','Indore','','',NULL),('Jamnagar','L2T25gnektDHexPVEnY8vQ==','Jamnagar','','',NULL),('Jamshedpur','9alrVejV1z6VUke7WOfTHA==','Jamshedpur','','',NULL),('Kerala','I43WYTLp18/58fDPfc78Tg==','Kerala','','',NULL),('Nagpur','IPyikCoABnohPBKOd+wOhA==','Nagpur','','',NULL),('Patna','bOPOiJliDdC5dc55cypE7w==','Patna','','',NULL),('Phoenix','wjDMO9J2SiG2e7LOiA3XRA==','Phoenix Mall','','',NULL),('Pune','GY973vFqh516dVnd0SUvtA==','Pune','','',NULL),('Punjagutta','Q/8MhmGJV2ekuGUr5L2lPw==','Punjagutta','','',NULL),('Raipur','MiBvwiQYUdQursM6gvAu7g==','Raipur','','',NULL),('Rajkot','rNtCGAAutV+aL/Rc8Mbvww==','Rajkot','','',NULL),('Ranchi','cpDf2wi/3NJWLd2eDCXGbQ==','Ranchi','','',NULL),('RCity','GzgGHQnpyiq3v8yyeuzFpw==','R City Mall','','',NULL),('SAI','aN8SPbQUZY44B0tXJ/W7tg==','Ahmedabad','89760785675','agasdgasdgvasbasdfgsag',NULL),('Santacruz','jrPQ0KLG+gdiL/HzWjAOOg==','Santacruz','','',NULL),('Seawoods','kCq0qRprtFJgtNmelJaX3A==','Seawoods','','',NULL),('Surat','tvDzd1m+y2oDDnQ6q1jytw==','Surat','','',NULL),('Thane','fLLPxDsynJRuajNxursTDw==','Thane','','',NULL),('Udaipur','UEfVuQzS7ezY1yLnLJPUgA==','Udaipur','','',NULL),('Vadodara','iWtkgYP7YUOplZiW6aENdg==','Vadodara','','',NULL),('Vapi','EiF7edf3op0f+deeAubJow==','Vapi','','',NULL),('Vasai','DpHDnJ0XYtIGhZ61lTKVIg==','Vasai','','',NULL),('Vashi','Ffux1USeq2VCv54hbxZO2g==','Vashi','','',NULL),('Vijaywada','ln3kfnQcSpJmJZOU7Jm+Aw==','Vijaywada','','',NULL),('Zaveri Bazar','miBzNvLN6aJx7QZYfou4cQ==','Zaveri Bazar','','',NULL);
/*!40000 ALTER TABLE `branch` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:48





details_category

-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `category`
--

DROP TABLE IF EXISTS `category`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `category` (
  `ID` int(11) DEFAULT NULL,
  `Name` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `category`
--

LOCK TABLES `category` WRITE;
/*!40000 ALTER TABLE `category` DISABLE KEYS */;
INSERT INTO `category` VALUES (1,'Plain Gold'),(2,'Studded Gold'),(3,'Premium Gold'),(4,'Diamond Jewellery'),(5,'Solitaire Jewellery'),(6,'Loose Diamonds'),(7,'Jadau'),(8,'Silver'),(10,'Platinum'),(11,'Coin');
/*!40000 ALTER TABLE `category` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:47





details_categorydetails


-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `categorydetails`
--

DROP TABLE IF EXISTS `categorydetails`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `categorydetails` (
  `FormNo` int(11) NOT NULL,
  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,
  `Category1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Item1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ItemConvStatus1` tinyint(1) DEFAULT NULL,
  `WalkOutReason1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Reason` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BudgetInGms` decimal(18,3) DEFAULT NULL,
  `TotalWeight` decimal(18,3) DEFAULT NULL,
  `LostGained` decimal(18,3) DEFAULT NULL,
  `PurchaseAmt` decimal(18,3) DEFAULT NULL,
  `Category2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Item2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ItemConvStatus2` tinyint(1) DEFAULT NULL,
  `WalkOutReason2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Reason2` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BudgetInGms2` decimal(18,3) DEFAULT NULL,
  `TotalWeight2` decimal(18,3) DEFAULT NULL,
  `LostGained2` decimal(18,3) DEFAULT NULL,
  `PurchaseAmt2` decimal(18,3) DEFAULT NULL,
  `Category3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Item3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ItemConvStatus3` tinyint(1) DEFAULT NULL,
  `WalkOutReason3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Reason3` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BudgetInGms3` decimal(18,3) DEFAULT NULL,
  `TotalWeight3` decimal(18,3) DEFAULT NULL,
  `LostGained3` decimal(18,3) DEFAULT NULL,
  `PurchaseAmt3` decimal(18,3) DEFAULT NULL,
  `Category4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Item4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ItemConvStatus4` tinyint(1) DEFAULT NULL,
  `WalkOutReason4` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Reason4` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BudgetInGms4` decimal(18,3) DEFAULT NULL,
  `TotalWeight4` decimal(18,3) DEFAULT NULL,
  `LostGained4` decimal(18,3) DEFAULT NULL,
  `PurchaseAmt4` decimal(18,3) DEFAULT NULL,
  `Category5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Item5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ItemConvStatus5` tinyint(1) DEFAULT NULL,
  `WalkOutReason5` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Reason5` varchar(300) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BudgetInGms5` decimal(18,3) DEFAULT NULL,
  `TotalWeight5` decimal(18,3) DEFAULT NULL,
  `LostGained5` decimal(18,3) DEFAULT NULL,
  `PurchaseAmt5` decimal(18,3) DEFAULT NULL,
  PRIMARY KEY (`FormNo`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `categorydetails`
--

LOCK TABLES `categorydetails` WRITE;
/*!40000 ALTER TABLE `categorydetails` DISABLE KEYS */;
INSERT INTO `categorydetails` VALUES (1,'CIT','Coin','CO',0,'Promo Offer available in competition','',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000),(2,'CIT',NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000),(3,'Ahm','Coin','CO',0,NULL,'',0.000,10.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000),(4,'Ahm',NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000,NULL,NULL,0,NULL,'',0.000,0.000,0.000,0.000),(5,'Ahm','','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(6,'Ahm','Coin','None',1,'None','',0.000,25.000,0.000,0.000,'Diamond Jewellery','None',0,'None','',0.000,35.000,0.000,0.000,'Jadau','None',0,'None','',0.000,22.000,0.000,0.000,'Loose Diamonds','None',0,'None','',0.000,36.000,0.000,0.000,'Plain Gold','None',0,'None','',0.000,15.000,0.000,0.000),(7,'Ahm','Platinum','None',0,'None','',0.000,26.000,0.000,0.000,'Premium Gold','None',0,'None','',0.000,40.000,0.000,0.000,'Silver','None',0,'None','',0.000,30.000,0.000,0.000,'Solitaire Jewellery','None',0,'None','',0.000,26.000,0.000,0.000,'Studded Gold','None',0,'None','',0.000,20.000,0.000,0.000),(8,'Ahm','Coin','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(9,'Ahm','Diamond Jewellery','None',0,'None','',12.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(10,'Ahm','Coin','None',1,'None','',3.000,4.000,0.333,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(11,'Ahm','Jadau','None',1,'None','',10.000,10.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(12,'Ahm','Coin','None',1,'None','jjjj',10.000,11.000,0.100,10.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000,'','None',0,'None','',0.000,0.000,0.000,0.000),(13,'Ahm','Coin','CO',0,'Old gold valuation','aaaaaaa',10.000,11.000,0.100,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000),(14,'Ahm','Coin','CO',1,'None','',10.000,11.000,0.100,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000,'','',0,'None','',0.000,0.000,0.000,0.000),(15,'Ahm','','None',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(16,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(17,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(18,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(19,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(20,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(21,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(22,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(23,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(24,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(25,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(26,'Ahm','','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000),(27,'Ahm','Coin','CO',1,'','na',10.000,11.000,0.100,10.000,'Diamond Jewellery','BA',0,'TBZ Gold Rate','na',10.000,10.000,0.000,20.000,'Jadau','BL',0,'','na',10.000,9.000,-0.100,30.000,'','',0,'','',0.000,0.000,0.000,0.000,'','',0,'','',0.000,0.000,0.000,0.000);
/*!40000 ALTER TABLE `categorydetails` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:48


details_community

-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `community`
--

DROP TABLE IF EXISTS `community`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `community` (
  `ID` int(11) DEFAULT NULL,
  `Name` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `community`
--

LOCK TABLES `community` WRITE;
/*!40000 ALTER TABLE `community` DISABLE KEYS */;
INSERT INTO `community` VALUES (1,'Assamese'),(2,'Bengali'),(3,'Catholic'),(4,'Gujarati'),(5,'Jain'),(6,'Jaiswal'),(7,'Maarwadi'),(8,'Marathi'),(9,'Muslim'),(10,'Oriya'),(11,'Others'),(12,'Punjabi'),(13,'Sindhi'),(14,'S-Indian'),(15,'UP/Bihari');
/*!40000 ALTER TABLE `community` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:49



details_entrydetails


-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `entrydetails`
--

DROP TABLE IF EXISTS `entrydetails`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `entrydetails` (
  `FormNo` int(11) NOT NULL,
  `UserId` varchar(50) CHARACTER SET utf8mb4 NOT NULL,
  `BranchName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `MobileNumber` varchar(10) CHARACTER SET utf8mb4 NOT NULL,
  `CustomerName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `NewOldCustomer` varchar(5) CHARACTER SET utf8mb4 DEFAULT NULL,
  `BrandLocation` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Community` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Occupation` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Gender` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Age` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Birthday` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Anniversary` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Occassion` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Area` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `EmailId` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `KP_Amount` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ShowroomConvSta` tinyint(1) DEFAULT NULL,
  `KP_Done` tinyint(1) DEFAULT NULL,
  `Address1` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Address2` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Address3` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `City` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Pincode` varchar(6) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Date` date DEFAULT NULL,
  `SAName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `SAEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,
  `DeptCRE` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `DeptEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,
  `TLName` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `TLEmpId` varchar(10) CHARACTER SET utf8mb4 DEFAULT NULL,
  `RefData1` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,
  `RefNumber1` varchar(12) CHARACTER SET utf8mb4 DEFAULT NULL,
  `RefData2` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,
  `RefNumber2` varchar(12) CHARACTER SET utf8mb4 DEFAULT NULL,
  `NoOfKP` varchar(500) CHARACTER SET utf8mb4 DEFAULT NULL,
  `KPWalkoutReason` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,
  `PreferredJew` varchar(50) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Hobbies` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL,
  `KnowTBZ` varchar(150) CHARACTER SET utf8mb4 DEFAULT NULL,
  `Details` varchar(200) CHARACTER SET utf8mb4 DEFAULT NULL,
  `ModeOfComm` varchar(10) DEFAULT NULL,
  `OtherMOCReason` varchar(150) CHARACTER SET utf8mb4 DEFAULT NULL,
  PRIMARY KEY (`FormNo`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `entrydetails`
--

LOCK TABLES `entrydetails` WRITE;
/*!40000 ALTER TABLE `entrydetails` DISABLE KEYS */;
INSERT INTO `entrydetails` VALUES (1,'CIT','Kolkata-CIT Road','9890899017','Santosh','Old','sa','Sindhi','NRI','Male','25-35',NULL,NULL,'Auspicious Buying','0-2.5 Kms','','',0,0,'','','','','','2018-09-07','','','','','','','','','','','','Will discuss with family','','','Radio','','--Select--',''),(2,'CIT','Kolkata-CIT Road','9890899517','test','Old','',NULL,NULL,'Male','Under 18',NULL,NULL,NULL,NULL,'','',0,0,'','','','','','2018-09-10','','','','','','','','','','','','Competitor has better scheme','','','Referred by friends / relatives','','SMS',''),(3,'Ahm','Ahmedabad','9890899517','Santosh','Old','',NULL,NULL,'Male','25-35',NULL,NULL,NULL,NULL,'','',0,0,'','','','','','2018-09-15','','','','','','','','','','','',NULL,'','','Hoardings','','Emails',''),(4,'Ahm','Ahmedabad','0989089951','Santosh','Old','',NULL,NULL,'Male','Under 18',NULL,NULL,NULL,NULL,'','',0,0,'','','','','','2018-09-25','','','','','','','','','','','',NULL,'','','Referred by friends / relatives','','Emails',''),(5,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(6,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(7,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(8,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','25-35','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(9,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(10,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-26','','','','','','','','','','','','','','','TV','','',''),(11,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(12,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(13,'Ahm','Ahmedabad','9969833641','Santosh','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(14,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','25-35','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(15,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(16,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(17,'Ahm','Ahmedabad','9969833641','Saiprasad','Old','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','Newspapers?','','',''),(18,'Ahm','Ahmedabad','9969833641','Saiprasad','Old','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(19,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(20,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(21,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(22,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Female','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(23,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','18-24','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(24,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(25,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-27','','','','','','','','','','','','','','','TV','','',''),(26,'Ahm','Ahmedabad','9969833641','Saiprasad','New','','','','Male','Under 18','','','','','saisawant29@gmail.com','',0,0,'','','','','','2018-09-29','','','','','','','','','','','','','','','TV','','',''),(27,'Ahm','Ahmedabad','1','a','New','','','','Male','Under 18','','','','','','1',0,0,'','','','','','2018-09-29','','','','','','','','','','','1','None','','','TV','','','');
/*!40000 ALTER TABLE `entrydetails` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:49




details_item

-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `item`
--

DROP TABLE IF EXISTS `item`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `item` (
  `Id` int(11) DEFAULT NULL,
  `Name` varchar(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `item`
--

LOCK TABLES `item` WRITE;
/*!40000 ALTER TABLE `item` DISABLE KEYS */;
INSERT INTO `item` VALUES (1,'AC'),(2,'BA'),(3,'BL'),(4,'CH'),(5,'CO'),(6,'DH'),(7,'E1'),(8,'EC'),(9,'EG'),(10,'KA'),(11,'MP'),(12,'MS'),(13,'NK'),(14,'NL'),(15,'NP'),(16,'P1'),(17,'PD'),(18,'PE'),(19,'PS'),(20,'R1'),(21,'RG'),(22,'SC'),(23,'SE'),(24,'TM'),(25,'LD'),(26,'Utensils'),(27,'Coins'),(28,'Idols'),(29,'Trays');
/*!40000 ALTER TABLE `item` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:48




details_occupation

-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `occupation`
--

DROP TABLE IF EXISTS `occupation`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `occupation` (
  `ID` int(11) DEFAULT NULL,
  `Name` varchar(100) CHARACTER SET utf8mb4 DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `occupation`
--

LOCK TABLES `occupation` WRITE;
/*!40000 ALTER TABLE `occupation` DISABLE KEYS */;
INSERT INTO `occupation` VALUES (1,'Builder'),(2,'Business'),(3,'CA'),(4,'Corporate'),(5,'Doctor'),(6,'Farmer'),(7,'Housewife'),(8,'Lawyer'),(9,'NRI'),(10,'Pensioner'),(11,'Politician'),(12,'Salaried');
/*!40000 ALTER TABLE `occupation` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:50



details_routines


-- MySQL dump 10.13  Distrib 5.7.17, for Win64 (x86_64)
--
-- Host: 172.25.4.71    Database: details
-- ------------------------------------------------------
-- Server version	5.7.17-log

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Dumping events for database 'details'
--

--
-- Dumping routines for database 'details'
--
/*!50003 DROP PROCEDURE IF EXISTS `insertCatDetails` */;
/*!50003 SET @saved_cs_client      = @@character_set_client */ ;
/*!50003 SET @saved_cs_results     = @@character_set_results */ ;
/*!50003 SET @saved_col_connection = @@collation_connection */ ;
/*!50003 SET character_set_client  = utf8 */ ;
/*!50003 SET character_set_results = utf8 */ ;
/*!50003 SET collation_connection  = utf8_general_ci */ ;
/*!50003 SET @saved_sql_mode       = @@sql_mode */ ;
/*!50003 SET sql_mode              = 'STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION' */ ;
DELIMITER ;;
CREATE DEFINER=`root`@`%` PROCEDURE `insertCatDetails`(
  p_formNo int,
  p_uid nvarchar(50),
  p_cat1 nvarchar(50)/* =null */,
  p_item1 nvarchar(50)/* =null */,
  p_itemConvSta1 tinyint/* =0 */,
  p_walkoutReason1 nvarchar(50)/* =null */,
  p_reason nvarchar(300)/* =null */,
  p_budginInGms decimal(18,3)/* =0 */,
  p_totalWt decimal(18,3)/* =0 */,
  p_lostgained decimal(18,3)/* =0 */,
  p_purchaseAmt decimal(18,3)/* =0 */,
  p_cat2 nvarchar(50)/* =null */,
  p_item2 nvarchar(50)/* =null */,
  p_itemConvSta2 tinyint/* =0 */,
  p_walkoutReason2 nvarchar(50)/* =null */,
  p_reason2 nvarchar(300)/* =null */,
  p_budginInGms2 decimal(18,3)/* =0 */,
  p_totalWt2 decimal(18,3)/* =0 */,
  p_lostgained2 decimal(18,3)/* =0 */,
  p_purchaseAmt2 decimal(18,3)/* =0 */,
  p_cat3 nvarchar(50)/* =null */,
  p_item3 nvarchar(50)/* =null */,
  p_itemConvSta3 tinyint/* =0 */,
  p_walkoutReason3 nvarchar(50)/* =null */,
  p_reason3 nvarchar(300)/* =null */,
  p_budginInGms3 decimal(18,3)/* =0 */,
  p_totalWt3 decimal(18,3)/* =0 */,
  p_lostgained3 decimal(18,3)/* =0 */,
  p_purchaseAmt3 decimal(18,3)/* =0 */,
  p_cat4 nvarchar(50)/* =null */,
  p_item4 nvarchar(50)/* =null */,
  p_itemConvSta4 tinyint/* =0 */,
  p_walkoutReason4 nvarchar(50)/* =null */,
  p_reason4 nvarchar(300)/* =null */,
  p_budginInGms4 decimal(18,3)/* =0 */,
  p_totalWt4 decimal(18,3)/* =0 */,
  p_lostgained4 decimal(18,3)/* =0 */,
  p_purchaseAmt4 decimal(18,3)/* =0 */,
  p_cat5 nvarchar(50)/* =null */,
  p_item5 nvarchar(50)/* =null */,
  p_itemConvSta5 tinyint/* =0 */,
  p_walkoutReason5 nvarchar(50)/* =null */,
  p_reason5 nvarchar(300)/* =null */,
  p_budginInGms5 decimal(18,3)/* =0 */,
  p_totalWt5 decimal(18,3)/* =0 */,
  p_lostgained5 decimal(18,3)/* =0 */,
  p_purchaseAmt5 decimal(18,3)/* =0 */

)
BEGIN
       INSERT INTO CATEGORYDETAILS
	   (
	        FormNo,
	        UserId
			,Category1
			,Item1
			,ItemConvStatus1
			,WalkOutReason1
			,Reason
			,BudgetInGms
			,TotalWeight
			,LostGained
			,PurchaseAmt
			,Category2
			,Item2
			,ItemConvStatus2
			,WalkOutReason2
			,Reason2
			,BudgetInGms2
			,TotalWeight2
			,LostGained2
			,PurchaseAmt2
			,Category3
			,Item3
			,ItemConvStatus3
			,WalkOutReason3
			,Reason3
			,BudgetInGms3
			,TotalWeight3
			,LostGained3
			,PurchaseAmt3
			,Category4
			,Item4
			,ItemConvStatus4
			,WalkOutReason4
			,Reason4
			,BudgetInGms4
			,TotalWeight4
			,LostGained4
			,PurchaseAmt4
			,Category5
			,Item5
			,ItemConvStatus5
			,WalkOutReason5
			,Reason5
			,BudgetInGms5
			,TotalWeight5
			,LostGained5
			,PurchaseAmt5



	   )
	   VALUES
	   (
	          p_formNo,
	          p_uid ,
			  p_cat1 ,
			  p_item1 ,
			  p_itemConvSta1 ,
			  p_walkoutReason1 ,
			  p_reason ,
			  p_budginInGms ,
			  p_totalWt ,
			  p_lostgained ,
			  p_purchaseAmt ,
			  p_cat2 ,
			  p_item2 ,
			  p_itemConvSta2 ,
			  p_walkoutReason2 ,
			  p_reason2 ,
			  p_budginInGms2 ,
			  p_totalWt2 ,
			  p_lostgained2 ,
			  p_purchaseAmt2 ,
			  p_cat3 ,
			  p_item3 ,
			  p_itemConvSta3 ,
			  p_walkoutReason3 ,
			  p_reason3 ,
			  p_budginInGms3 ,
			  p_totalWt3 ,
			  p_lostgained3 ,
			  p_purchaseAmt3 ,
			  p_cat4 ,
			  p_item4 ,
			  p_itemConvSta4 ,
			  p_walkoutReason4 ,
			  p_reason4 ,
			  p_budginInGms4 ,
			  p_totalWt4 ,
			  p_lostgained4 ,
			  p_purchaseAmt4 ,
			  p_cat5 ,
			  p_item5 ,
			  p_itemConvSta5 ,
			  p_walkoutReason5 ,
			  p_reason5 ,
			  p_budginInGms5 ,
			  p_totalWt5 ,
			  p_lostgained5 ,
			  p_purchaseAmt5 
	   );

END ;;
DELIMITER ;
/*!50003 SET sql_mode              = @saved_sql_mode */ ;
/*!50003 SET character_set_client  = @saved_cs_client */ ;
/*!50003 SET character_set_results = @saved_cs_results */ ;
/*!50003 SET collation_connection  = @saved_col_connection */ ;
/*!50003 DROP PROCEDURE IF EXISTS `insertEntryDetails` */;
/*!50003 SET @saved_cs_client      = @@character_set_client */ ;
/*!50003 SET @saved_cs_results     = @@character_set_results */ ;
/*!50003 SET @saved_col_connection = @@collation_connection */ ;
/*!50003 SET character_set_client  = utf8 */ ;
/*!50003 SET character_set_results = utf8 */ ;
/*!50003 SET collation_connection  = utf8_general_ci */ ;
/*!50003 SET @saved_sql_mode       = @@sql_mode */ ;
/*!50003 SET sql_mode              = 'STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION' */ ;
DELIMITER ;;
CREATE DEFINER=`root`@`%` PROCEDURE `insertEntryDetails`(
  p_formNo int,
  p_uid nvarchar(50),
  p_branchName nvarchar(50),
  p_mobileNo nvarchar(10),
  p_custName nvarchar(50),
  p_newoldCust nvarchar(5),
  p_brandLocation nvarchar(50)/* =null */,
  p_community nvarchar(50)/* =null */,
  p_occupation nvarchar(50)/* =null */,
  p_gender nvarchar(10),
  p_age nvarchar(100),
  p_birthday nvarchar(50)/* =null */,
  p_anniversary nvarchar(200)/* =null */,
  p_occassion nvarchar(50)/* =null */,
  p_area nvarchar(50)/* =null */,
  p_email nvarchar(50)/* =null */,
  p_kpAmount nvarchar(50)/* =null */,
  p_showconvsta tinyint/* =0 */,
  p_kpdone tinyint/* =0 */,
  p_address1 nvarchar(50)/* =null */,
  p_address2 nvarchar(50)/* =null */,
  p_address3 nvarchar(50)/* =null */,
  p_city nvarchar(50)/* =null */,
  p_pincode nvarchar(6)/* =null */,
  p_enterdate date/* =null */,
  p_saname nvarchar(50)/* =null */,
  p_saempid nvarchar(10)/* =null */,
  p_deptcre nvarchar(50)/* =null */,
  p_deptempid nvarchar(10)/* =null */,
  p_tlname nvarchar(50)/* =null */,
  p_tlempid nvarchar(10)/* =null */,
  p_refData1 nvarchar(200)/* =null */,
  p_refNumber1 nvarchar(12)/* =null */,
  p_refData2 nvarchar(200)/* =null */,
  p_refNumber2 nvarchar(12)/* =null */,
  p_noofkp nvarchar(500)/* =null */,
  p_kpwalkoutreason nvarchar(200)/* =null */,
  p_prefJew nvarchar(50)/* =null */,
  p_hobbies nvarchar(100)/* =null */,
  p_knowTbz nvarchar(150)/* =null */,
  p_details nvarchar(200)/* =null */,
  p_mofc varchar(10)/* =null */,
  p_otherMOCReason nvarchar(150)/* =null */

)
BEGIN
		INSERT INTO EntryDetails(
		FormNo,
		UserId,
		BranchName,
		MobileNumber,
		CustomerName,
		NewOldCustomer,
		BrandLocation,
		Community,
		Occupation,
		Gender,
		Age,
		Birthday,
		Anniversary,
		Occassion,
		Area,
		EmailId,
		KP_Amount,
		ShowroomConvSta,
		KP_Done,
		Address1,
		Address2,
		Address3,
		City,
		Pincode,
		Date,
		SAName,
		SAEmpId,
		DeptCRE,
		DeptEmpId,
		TLName,
		TLEmpId,
		RefData1,
		RefNumber1,
		RefData2,
		RefNumber2,
		NoOfKP,
		KPWalkoutReason,
		PreferredJew,
		Hobbies,
		KnowTBZ,
		Details,
		ModeOfComm,
		OtherMOCReason
		)
		VALUES
		(
		  p_formNo,
		  p_uid,
		  p_branchName ,
		  p_mobileNo ,
		  p_custName ,
		  p_newoldCust ,
		  p_brandLocation ,
		  p_community ,
		  p_occupation,
		  p_gender ,
		  p_age ,
		  p_birthday ,
		  p_anniversary ,
		  p_occassion ,
		  p_area ,
		  p_email ,
		  p_kpAmount ,
		  p_showconvsta ,
		  p_kpdone ,
		  p_address1 ,
		  p_address2 ,
		  p_address3 ,
		  p_city ,
		  p_pincode ,
		  p_enterdate ,
		  p_saname ,
		  p_saempid ,
		  p_deptcre ,
		  p_deptempid ,
		  p_tlname ,
		  p_tlempid,
		  p_refData1 ,
		  p_refNumber1 ,
		  p_refData2 ,
		  p_refNumber2 ,
		  p_noofkp ,
		  p_kpwalkoutreason ,
		  p_prefJew ,
		  p_hobbies ,
		  p_knowTbz,
		  p_details,
          p_mofc,
		  p_otherMOCReason
		);
END ;;
DELIMITER ;
/*!50003 SET sql_mode              = @saved_sql_mode */ ;
/*!50003 SET character_set_client  = @saved_cs_client */ ;
/*!50003 SET character_set_results = @saved_cs_results */ ;
/*!50003 SET collation_connection  = @saved_col_connection */ ;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2018-09-29 16:56:51





