find dois on simple text form first before running the script

import requests
import pandas as pd
import time

# 正确的配对
correct_pairs = [
    ("United Nations. World population ageing report. New York: United Nations; 2022.", None),
    ("Bloom DE, Chatterji S, Kowal P, Lloyd-Sherlock P, McKee M, Rechel B, et al. Macroeconomic implications of population ageing and selected policy responses. Lancet. 2015;385(9968):649-57.", "10.1016/S0140-6736(14)61464-1"),
    ("Harper S. Economic and social implications of aging societies. Science. 2014;346(6209):587-91.", "10.1126/science.1254405"),
    ("Prince M, Wimo A, Guerchet M, Ali GC, Wu YT, Prina M. World Alzheimer Report 2015: The Global Impact of Dementia. London: Alzheimer's Disease International; 2015.", None),
    ("Livingston G, Huntley J, Sommerlad A, Ames D, Ballard C, Banerjee S, et al. Dementia prevention, intervention, and care: 2020 report of the Lancet Commission. Lancet. 2020;396(10248):413-46.", "10.1016/S0140-6736(20)30367-6"),
    ("Beard JR, Officer A, de Carvalho IA, Sadana R, Pot AM, Michel JP, et al. The World report on ageing and health: a policy framework for healthy ageing. Lancet. 2016;387(10033):2145-54.", "10.1016/S0140-6736(15)00516-4"),
    ("Christensen K, Doblhammer G, Rau R, Vaupel JW. Ageing populations: the challenges ahead. Lancet. 2009;374(9696):1196-208.", "10.1016/S0140-6736(09)61460-4"),
    ("World Health Organization. Global status report on the public health response to dementia. Geneva: World Health Organization; 2021.", None),
    ("Wimo A, Guerchet M, Ali GC, Wu YT, Prina AM, Winblad B, et al. The worldwide costs of dementia 2015 and comparisons with 2010. Alzheimers Dement. 2017;13(1):1-7.", "10.1016/j.jalz.2016.07.150"),
    ("Hurd MD, Martorell P, Delavande A, Mullen KJ, Langa KM. Monetary costs of dementia in the United States. N Engl J Med. 2013;368(14):1326-34.", "10.1056/NEJMsa1204629"),
    ("Alzheimer's Association. 2024 Alzheimer's disease facts and figures. Alzheimers Dement. 2024;20(5):3708-821.", "10.1002/alz.13809"),
    ("Lang L, Clifford A, Wei L, Zhang D, Leung D, Augustine G, et al. Prevalence and determinants of undetected dementia in the community: a systematic literature review and a meta-analysis. BMJ Open. 2017;7(2):e011146.", "10.1136/bmjopen-2016-011146"),
    ("Dubois B, Villain N, Frisoni GB, Rabinovici GD, Sabbagh M, Cappa S, et al. Clinical diagnosis of Alzheimer's disease: recommendations of the International Working Group. Lancet Neurol. 2021;20(6):484-96.", "10.1016/S1474-4422(21)00066-1"),
    ("Cummings J, Lee G, Ritter A, Sabbagh M, Zhong K. Alzheimer's disease drug development pipeline: 2019. Alzheimers Dement (N Y). 2019;5:272-93.", "10.1016/j.trci.2019.05.008"),
    ("Dubois B, Hampel H, Feldman HH, Scheltens P, Aisen P, Andrieu S, et al. Preclinical Alzheimer's disease: definition, natural history, and diagnostic criteria. Alzheimers Dement. 2016;12(3):292-323.", "10.1016/j.jalz.2016.02.002"),
    ("Brookmeyer R, Johnson E, Ziegler-Graham K, Arrighi HM. Forecasting the global burden of Alzheimer's disease. Alzheimers Dement. 2007;3(3):186-91.", "10.1016/j.jalz.2007.04.381"),
    ("Sperling RA, Aisen PS, Beckett LA, Bennett DA, Craft S, Fagan AM, et al. Toward defining the preclinical stages of Alzheimer's disease: recommendations from the National Institute on Aging-Alzheimer's Association workgroups on diagnostic guidelines for Alzheimer's disease. Alzheimers Dement. 2011;7(3):280-92.", "10.1016/j.jalz.2011.03.003"),
    ("Jack CR Jr, Bennett DA, Blennow K, Carrillo MC, Dunn B, Haeberlein SB, et al. NIA-AA Research Framework: toward a biological definition of Alzheimer's disease. Alzheimers Dement. 2018;14(4):535-62.", "10.1016/j.jalz.2018.02.018"),
    ("Bradford A, Kunik ME, Schulz P, Williams SP, Singh H. Missed and delayed diagnosis of dementia in primary care: prevalence and contributing factors. Alzheimer Dis Assoc Disord. 2009;23(4):306-14.", "10.1097/WAD.0b013e3181a6bebc"),
    ("Pentzek M, Wollny A, Wiese B, Jessen F, Haller F, Maier W, et al. Apart from nihilism and ageism: what influences general practitioners' accuracy in identifying incident dementia? Am J Geriatr Psychiatry. 2009;17(11):965-75.", "10.1097/JGP.0b013e3181b2075e"),
    ("Valcour VG, Masaki KH, Curb JD, Blanchette PL. The detection of dementia in the primary care setting. Arch Intern Med. 2000;160(19):2964-8.", "10.1001/archinte.160.19.2964"),
    ("Robinson L, Tang E, Taylor JP. Dementia: timely diagnosis and early intervention. BMJ. 2015;350:h3029.", "10.1136/bmj.h3029"),
    ("Borson S, Frank L, Bayley PJ, Boustani M, Dean M, Lin PJ, et al. Improving dementia care: the role of screening and detection of cognitive impairment. Alzheimers Dement. 2013;9(2):151-9.", "10.1016/j.jalz.2012.08.008"),
    ("Boustani M, Peterson B, Hanson L, Harris R, Lohr KN; U.S. Preventive Services Task Force. Screening for dementia in primary care: a summary of the evidence for the U.S. Preventive Services Task Force. Ann Intern Med. 2003;138(11):927-37.", "10.7326/0003-4819-138-11-200306030-00015"),
    ("Koschel A, Oelke ND, Lavoie AM, Purden M. Rural-urban differences in the availability of mental health providers. Rural Remote Health. 2012;12:2174.", None),
    ("Morgan DG, Crossley M, Kirk A, D'Arcy C, Stewart N, Biem J, et al. Improving access to dementia care: development and evaluation of a rural and remote memory clinic. Aging Ment Health. 2009;13(1):17-30.", "10.1080/13607860802154432"),
    ("Andrilla CHA, Patterson DG, Garberson LA, Coulthard C, Larson EH. Geographic variation in the supply of selected behavioral health providers. Am J Prev Med. 2018;54(6 Suppl 3):S199-207.", "10.1016/j.amepre.2018.01.004"),
    ("Koller D, Bynum JPW. Dementia in the USA: state variation in prevalence. J Public Health (Oxf). 2015;37(4):597-604.", None),
    ("American Academy of Neurology. Practice parameter: diagnosis of dementia (an evidence-based review). Report of the Quality Standards Subcommittee of the American Academy of Neurology. Neurology. 2001;56(9):1143-53.", "10.1212/WNL.56.9.1143"),
    ("Lezak MD, Howieson DB, Bigler ED, Tranel D. Neuropsychological Assessment. 5th ed. New York: Oxford University Press; 2012.", None),
    ("Petersen RC, Stevens JC, Ganguli M, Tangalos EG, Cummings JL, DeKosky ST. Practice parameter: early detection of dementia: mild cognitive impairment (an evidence-based review). Report of the Quality Standards Subcommittee of the American Academy of Neurology. Neurology. 2001;56(9):1133-42.", "10.1212/WNL.56.9.1133"),
    ("Knopman DS, DeKosky ST, Cummings JL, Chui H, Corey-Bloom J, Relkin N, et al. Practice parameter: diagnosis of dementia (an evidence-based review). Report of the Quality Standards Subcommittee of the American Academy of Neurology. Neurology. 2001;56(9):1143-53.", "10.1212/WNL.56.9.1143"),
    ("Harvey PD. Clinical applications of neuropsychological assessment. Dialogues Clin Neurosci. 2012;14(1):91-9.", "10.31887/DCNS.2012.14.1/pharvey"),
    ("Bondi MW, Edmonds EC, Jak AJ, Clark LR, Delano-Wood L, McDonald CR, et al. Neuropsychological criteria for mild cognitive impairment improves diagnostic precision, biomarker associations, and progression rates. J Alzheimers Dis. 2014;42(1):275-89.", "10.3233/JAD-140276"),
    ("Sweet JJ, Benson LM, Nelson NW, Moberg PJ. The American Academy of Clinical Neuropsychology, National Academy of Neuropsychology, and Society for Clinical Neuropsychology (APA Division 40) 2015 TCN professional practice and 'salary survey': professional practices, beliefs, and incomes of US neuropsychologists. Clin Neuropsychol. 2015;29(8):1069-162.", "10.1080/13854046.2016.1140228"),
    ("Bieliauskas LA, Fastenau PS, Lacy MA, Roper BL. Use of the National Academy of Neuropsychology Professional Practices Survey in assessing neuropsychology practice and training. Arch Clin Neuropsychol. 2017;32(3):293-305.", None),
    ("Dall TM, Storm MV, Chakrabarti R, Drogan O, Keran CM, Donofrio PD, et al. Supply and demand analysis of the current and future US neurology workforce. Neurology. 2013;81(5):470-8.", "10.1212/WNL.0b013e318294b1cf"),
    ("Institute of Medicine. Retooling for an aging America: building the health care workforce. Washington, DC: The National Academies Press; 2008.", None),
    ("Warshaw GA, Bragg EJ, Shaull RW, Lindsell CJ. Academic geriatric medicine: strategies to address workforce needs. Acad Med. 2002;77(4):313-6.", None),
    ("Reuben DB, Fullerton JT, Tschann JM, Croughan-Minihane M. Attitudes of beginning medical students toward older persons: a five-campus study. The University of California Academic Geriatric Resource Program Student Survey Research Group. J Am Geriatr Soc. 1995;43(12):1430-6.", "10.1111/j.1532-5415.1995.tb06626.x"),
    ("Ono T, Lafortune G, Schoenstein M. Health workforce planning in OECD countries: a review of 26 projection models from 18 countries. OECD Health Working Papers, No. 62. Paris: OECD Publishing; 2013.", None),
    ("Scheffler RM, Campbell J, Cometto G, Maeda A, Liu J, Bruckner TA, et al. Forecasting imbalances in the global health labor market and devising policy responses. Hum Resour Health. 2018;16(1):5.", "10.1186/s12960-017-0264-6"),
    ("Spetz J, Bates T. Is a registered nurse shortage on the horizon? Final report to the California HealthCare Foundation. San Francisco: UCSF Health Workforce Research Center on Long-Term Care; 2013.", None),
    ("Liu JL, Hlavka JP, Hillestad R, Mattke S. Assessing the preparedness of the U.S. health care system infrastructure for an Alzheimer's treatment. Santa Monica, CA: RAND Corporation; 2017.", "10.7249/RR2272"),
    ("Gaugler JE, Reimer MA, Roth DL. Dementia care workforce development: priority areas for research, policy, and practice. Gerontologist. 2022;62(4):e193-204.", None),
    ("Reilly S, Miranda-Castillo C, Malouf R, Hoe J, Toot S, Challis D, et al. Case management approaches to home support for people with dementia. Cochrane Database Syst Rev. 2015;(1):CD008345.", "10.1002/14651858.CD008345.pub2"),
    ("Fulmer T, Mate KS, Berman A. The age-friendly health system imperative. J Am Geriatr Soc. 2018;66(1):22-4.", "10.1111/jgs.15076"),
    ("U.S. Department of Health and Human Services. National Plan to Address Alzheimer's Disease: 2021 Update. Washington, DC: U.S. Department of Health and Human Services; 2021.", None),
    ("Centers for Medicare & Medicaid Services. Medicare coverage of innovative technology (MCIT) and definition of 'reasonable and necessary'. Baltimore, MD: Centers for Medicare & Medicaid Services; 2022.", None),
]

print("="*80)
print("用 requests 库直接调用 Crossref REST API")
print("="*80)

verification_results = []

print(f"\n开始查询 {len(correct_pairs)} 个 references...")
print("(这可能需要 2-3 分钟)\n")

for i, (ref_text, doi) in enumerate(correct_pairs):
    if (i + 1) % 10 == 0:
        print(f"进度: {i+1}/{len(correct_pairs)}")
    
    result = {
        'ref_number': i + 1,
        'manuscript_text': ref_text,
        'doi': doi,
        'crossref_title': '',
        'crossref_authors': '',
        'crossref_year': '',
        'crossref_doi_url': '',
        'status': 'No DOI' if not doi else 'Processing'
    }
    
    if doi:
        try:
            # 直接调用 Crossref REST API
            url = f"https://api.crossref.org/v1/works/{doi}"
            headers = {'User-Agent': 'MyApp/1.0'}
            
            response = requests.get(url, headers=headers, timeout=10)
            
            if response.status_code == 200:
                data = response.json()
                
                if 'message' in data:
                    msg = data['message']
                    result['status'] = 'Found'
                    result['crossref_title'] = msg.get('title', [''])[0] if msg.get('title') else ''
                    result['crossref_year'] = msg.get('published-print', {}).get('date-parts', [[]])[0][0] if msg.get('published-print') else ''
                    result['crossref_doi_url'] = f"https://doi.org/{doi}"
                    
                    # 提取作者
                    authors = msg.get('author', [])
                    if authors:
                        author_names = [f"{a.get('family', '')} {a.get('given', '')}" for a in authors[:5]]
                        result['crossref_authors'] = '; '.join(author_names)
            else:
                result['status'] = f'HTTP {response.status_code}'
        
        except Exception as e:
            result['status'] = f'Error: {str(e)[:30]}'
    
    verification_results.append(result)
    time.sleep(0.5)  # 礼貌地延迟请求

print(f"\n✓ 查询完成！")

df_results = pd.DataFrame(verification_results)

# 显示摘要
found = len(df_results[df_results['status'] == 'Found'])
no_doi = len(df_results[df_results['status'] == 'No DOI'])
errors = len(df_results[~df_results['status'].isin(['Found', 'No DOI'])])

print(f"\n总 References: {len(df_results)}")
print(f"✓ 有 DOI 且成功查询: {found}")
print(f"✗ 无 DOI: {no_doi}")
print(f"⚠ 查询出错: {errors}")

# 保存到 Excel
output_file = 'reference_verification_with_metadata.xlsx'

with pd.ExcelWriter(output_file, engine='openpyxl') as writer:
    # 只保留有用的列
    display_cols = ['ref_number', 'manuscript_text', 'doi', 'crossref_title', 'crossref_authors', 'crossref_year', 'crossref_doi_url', 'status']
    df_results[display_cols].to_excel(writer, sheet_name='All Results', index=False)
    
    # 有 DOI 的
    with_doi = df_results[df_results['status'] == 'Found']
    with_doi[display_cols].to_excel(writer, sheet_name='With Metadata', index=False)
    
    # 无 DOI 的
    without_doi = df_results[df_results['status'] == 'No DOI']
    without_doi[display_cols].to_excel(writer, sheet_name='Without DOI', index=False)

print(f"\n✓ 报告已保存: {output_file}")
print("\n说明：")
print("- 'manuscript_text' = 你的 manuscript 中的 reference")
print("- 'crossref_*' = Crossref 数据库中的真实信息")
print("- 对比两边看是否一致")
