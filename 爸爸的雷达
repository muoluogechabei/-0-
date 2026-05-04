import os
from datetime import datetime, timedelta
from Bio import Entrez

# 填个邮箱告诉数据库你是谁
Entrez.email = "medical_researcher@example.com" 

# 这里我直接帮你预设了精准的检索词
QUERY = '("Atrial Fibrillation"[Title/Abstract]) AND ("Liver Fibrosis"[Title/Abstract] OR "Metabolomics"[Title/Abstract])'

def fetch_papers():
    today = datetime.now()
    yesterday = today - timedelta(days=1)
    date_str = yesterday.strftime("%Y/%m/%d")

    # 搜索过去24小时内的新文献
    search_query = f"{QUERY} AND {date_str}[EDAT]"
    handle = Entrez.esearch(db="pubmed", term=search_query, retmax=10)
    record = Entrez.read(handle)
    handle.close()

    id_list = record["IdList"]
    if not id_list:
        return f"# 今日文献雷达 ({today.strftime('%Y-%m-%d')})\n\n今日暂无相关新文献更新。多休息一天！\n"

    # 获取详情
    handle = Entrez.efetch(db="pubmed", id=id_list, retmode="xml")
    records = Entrez.read(handle)
    handle.close()

    markdown_content = f"# 今日文献雷达 ({today.strftime('%Y-%m-%d')})\n\n"
    for article in records.get('PubmedArticle', []):
        try:
            medline = article['MedlineCitation']
            title = medline['Article']['ArticleTitle']
            pmid = medline['PMID']
            url = f"https://pubmed.ncbi.nlm.nih.gov/{pmid}/"
            abstract_list = medline['Article'].get('Abstract', {}).get('AbstractText', ['无摘要'])
            abstract = abstract_list[0] if abstract_list else "无摘要"

            markdown_content += f"## {title}\n"
            markdown_content += f"**链接**: {url}\n\n"
            markdown_content += f"**摘要**: {abstract}\n\n---\n"
        except Exception as e:
            continue

    return markdown_content

if __name__ == "__main__":
    content = fetch_papers()
    # 自动保存到 outputs/context 目录
    os.makedirs("outputs/context", exist_ok=True)
    filename = f"outputs/context/daily_context_{datetime.now().strftime('%Y-%m-%d')}.md"
    with open(filename, "w", encoding="utf-8") as f:
        f.write(content)
