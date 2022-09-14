#coding:utf-8
import maya.cmds as cmds
import time


def rt():
	if(cmds.window('re_ref_win',q=True,ex=True)):cmds.deleteUI('re_ref_win')
	cmds.window('re_ref_win')
	cmds.formLayout('main_formlay')
	cmds.textFieldButtonGrp('folder_txbtn',cw=[[1, 90]],adj=2,l=u'Maya File Folder: ',bl=u'<<<<',bc="get_files()")
	cmds.treeView('treeview_list')
	cmds.button('run_btn',h=30,l=u'Run',c="_run()")
	cmds.formLayout('main_formlay',e=1,af=[['folder_txbtn', 'top', 10], 
                                        ['folder_txbtn', 'left', 5], 
                                        ['folder_txbtn', 'right', 5], 
                                        ['treeview_list', 'left', 5], 
                                        ['treeview_list', 'right', 5], 
                                        ['run_btn', 'left', 5], 
                                        ['run_btn', 'right', 5], 
                                        ['run_btn', 'bottom', 10]],
                                    ac=[['treeview_list', 'top', 10, 'folder_txbtn'], 
                                    ['treeview_list', 'bottom', 10, 'run_btn']])
	cmds.showWindow('re_ref_win')

rt()


def get_files():
    fileFolderPath = cmds.fileDialog2(dialogStyle=1, fileMode=3)[0]
    print (fileFolderPath)
    if fileFolderPath:
        list_files = search_files(fileFolderPath)
        cmds.textFieldButtonGrp('folder_txbtn',e=True,tx=fileFolderPath)
        if len(list_files):
            for f in list_files:
                treeViewShow(f,fileFolderPath)

def treeViewShow(name,folder_path):
    cmds.treeView('treeview_list',e=True,ai =(os.path.splitext(name)[0],""))
    cmds.treeView('treeview_list',e=True,ia=(os.path.splitext(name)[0],("{}/{}".format(folder_path,name))))
    
def search_files(folder_path):
    list_files = os.listdir(folder_path) 
    list_files = list(filter(file_filter, list_files))
    
    if len(list_files) :
        return list_files
    else:
        return False

def file_filter(file_name):
    mayaFilters = ['.ma','.mb','.MA','.MB']
    fbxFilters = ['.fbx','.FBX']
    if file_name[-3:] in mayaFilters or file_name[-4:] in fbxFilters :
        return True
    else:
        return False   
                    
def replace_reference():
    
    ref_nodes = cmds.ls(type="reference")
    
    patt=['rig','model']
    path_patt = ['model','animation']
    target_patt = r'render'

    for ref_node in ref_nodes:
        
        new_file = ''
        
        isload_ref = cmds.referenceQuery(ref_node, il=True)
        

        if isload_ref:
            ref_filepath = cmds.referenceQuery(ref_node, f=True, wcn=True)
            (filepath,filename) = os.path.split(ref_filepath)
            
            if filepath.split("/")[-1] in path_patt:
                if re.compile(filepath.split("/")[-1]).findall(ref_filepath):
                    ref_file = filepath.replace(filepath.split("/")[-1],target_patt)
            if os.path.splitext(filename)[0].split("_")[-1] in patt:
                if re.compile(os.path.splitext(filename)[0].split("_")[-1]).findall(filename):
                    new_file_name = filename.replace(os.path.splitext(filename)[0].split("_")[-1],target_patt)
                    new_file = "%s/%s" %(ref_file,new_file_name)
                    print (new_file)
            if os.path.exists(new_file):
                cmds.file(new_file, lr=ref_node)
                print ("convert ref: {}, Finish".format(ref_node))
                mel.eval("print \"" + new_file + u" Finish .\";")
            else :
                mel.eval("print \"" + new_file + u" Not This File.\";")
            
            time.sleep(0.5)
                
    file_name = cmds.file(q=True,sn=True)
    cmds.file(rn=file_name)
    cmds.file(f=True,s=True)

                    
                    
                    
def _run():
    treeview_all_item = cmds.treeView("treeview_list",q=True,ch=True)
    all_items = []
    for item in treeview_all_item:
        all_items.append([item,cmds.treeView("treeview_list",q=True,it=item,ia=True)])
        
    for v in range(len(all_items)):
        if os.path.splitext(all_items[v][1])[1] == ".ma":
            file_type = "mayaAscii"
        else:
            file_type = "mayaBinary"
            
        cmds.file(all_items[v][1],f=True,o=True,iv=True,typ=file_type,op="v=0;")
        
        replace_reference()
        time.sleep(0.1)
