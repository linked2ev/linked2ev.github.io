

---

```js
  /**
         * 라운드 기준 평가자, 대상자 평가그룹 중복 조회
         * @param employee
         * @param type
         */
        evaGroupDuplicate: function (employeeList, apsType){
            return new Promise((resolve, object) => {
                let employeeIds = [];
                for (let e of employeeList) {
                    employeeIds.push(e.employeeId * 1);
                }
                let rvDup = true;
                let params = {
                    employeeIds: employeeIds,
                    apsType: apsType      //평가자, 대상자 구분
                    //, evaType: this.evaType //LEADER_EVA
                }

                AjaxUtil.get({
                    url: '/employer/assessment-group/api/duplicate/'+ this.roundId,
                    param: params,
                    success: res => {
                        //console.log('>> [해당 라운드 평가 그룹] 중복 확인=', res);
                        if ('success' != res.message) {
                            LayerUtil.alert(res.message);
                            resolve(false);
                        }
                        resolve(true);
                    }
                });
            })
        },


//


 let rvDup = await this.evaGroupDuplicate(this.employeeActivatedList, 'APSE');
            if (! rvDup) return;

            
            ```

