from pathlib import Path
import re, json

# Reuse the original full question list stored in the notebook variable if available.
# Correct filter: keep PPT 1 all, and PPT 2 ids 2.1 through 2.30.
def id_num_parts(qid):
    a, b = qid.split(".")
    return int(a), int(b)

questions70 = []
for q in questions:  # 'questions' still contains the original 76-item list in this session
    a, b = id_num_parts(q["id"])
    if a == 1:
        questions70.append(q)
    elif a == 2 and b <= 30:
        questions70.append(q)

path = Path("/mnt/data/simulador_ia_local_turbomaquinas_hidroelectricas.html")
text = path.read_text(encoding="utf-8")
new_text = re.sub(
    r"const QUESTIONS = \[.*?\];\n\nconst norm",
    "const QUESTIONS = " + json.dumps(questions70, ensure_ascii=False, indent=2) + ";\n\nconst norm",
    text,
    flags=re.S
)
path.write_text(new_text, encoding="utf-8")
path.as_posix(), len(questions70), sum(q["id"].startswith("1.") for q in questions70), sum(q["id"].startswith("2.") for q in questions70)
