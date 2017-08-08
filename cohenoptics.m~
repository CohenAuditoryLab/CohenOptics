function cohenoptics = cohenoptics(binary_file, rez_file, datadir, optics_folder)
    % variables    
    if exist('binary_file', 'var') == 0
        binary_file = '/Users/mschaff/Documents/KiloSort/Jun22_17_192ch_sr25kblock4__binary.dat';
    end
    if exist('rez_file', 'var') == 0
        rez_file = '/Users/mschaff/Documents/KiloSort/rez.mat';
    end
    if exist('datadir', 'var') == 0
        datadir = '/Users/mschaff/Documents/testwhat';
    end
    if exist('optics_folder', 'var') == 0
        optics_folder = '/Users/mschaff/Documents/REPOS/PennSort/Clustering';
    end
    
    if(ispc)
        slash = '\';
    else
        slash = '/';
    end
    %load rez & get raw waveforms
    load(rez_file);
    disp('Extracting raw waveforms.');
    out = getRawWaveforms(rez, binary_file);
    % optics prep
    optics_minpts = 10;
    optics_eps = 1e6;

    if ~exist([datadir slash 'OPTICSfiles'],'dir')
        mkdir([datadir slash 'OPTICSfiles']);
    end
    optics_file_prefix = [datadir 'OPTICSfiles' slash 'temp'];
    peakch = out.peakChannel;
    ind = [];
    for i=1:numel(unique(peakch))%m=unique(peakch)
        m = peakch(i);
        spikes = find(peakch == m);
        %disp(['Sorting neighborhood of channel ',num2str(m),'...']);
        disp(num2str(m));
        %spikes = initind(markers(m):markers(m+1)-1);
        %spikes = find(peakch == m)';
        %     r_euclid = zeros(length(spikes));
        %     for i=1:length(spikes)
        %         for j=1:(i-1)
        %             r_euclid(i,j) = norm(S_masked(spikes(i),:)-S_masked(spikes(j),:));
        % %             r_euclid(i,j) = norm(S_masked(spikes(i),:)-S_masked(spikes(j),:),inf);
        %             r_euclid(j,i) = r_euclid(i,j);
        %         end
        %     end

            % *** Run OPTICS ***
        %    r_euclid = r_euclid + tril(r_euclid,-1)';
            dim = size(out.spikeWaves, 2);%size(S_masked,2);
            num_points = length(spikes);
            ascii_file_name = [optics_file_prefix num2str(m) '.ascii'];
            dump_file_name = [optics_file_prefix num2str(m) '.dump'];
            optics_file_name = [optics_file_prefix num2str(m) '.opt'];

            % Save spikes out in ascii format.
            write_ascii_for_optics(ascii_file_name, out.spikeWaves(m, :, spikes));      
            %convert .ascii to .dump file
            command = [[optics_folder,'ascii2dump'],' ', ascii_file_name,' ', num2str(dim),' ', dump_file_name];
            system(command);    
            %run optics on the .dump file -> .opt file as result
            command = [optics_folder,'optics',' ', '-s',' ', num2str(optics_eps,'%2.0e'),' ', num2str(optics_minpts), ' '...
                , num2str(dim),' ', num2str(num_points),' ',dump_file_name, ' ', optics_file_name];
            system(command);

            % Load .opt output to get ordering.
            str = [];
            for i = 1:dim
                str = [str, '%f ' ];
            end
            str = [str, '%f %f \"%d\"'];

            fid = fopen(optics_file_name,'r');
            OPTICS_output = fscanf(fid,str,[dim + 3,inf]);  
            ind_m = OPTICS_output(dim+3,:)+1;
            clear OPTICS_output;
            fclose(fid);

          % ind_m = poormansoptics(r_euclid);
        %    clu_local = dbscan(r_euclid,epsilon,nmin);

        %     ind_m = [];
        %     for i=1:length(clu_local)
        %         clu = [clu,{spikes(clu_local{i})}];
        %         ind_m = [ind_m,clu_local{i}];
        %     end
            %subplot(1,2,1);
            %imagesc(-S(spikes(ind_m),:)',[-50,500]); drawnow;
            %subplot(1,2,2); 
            %imagesc(r_euclid(ind_m,ind_m));drawnow; pause;
        %    hist(r_euclid(:),500); drawnow; pause;
        %    plot(S_masked(spikes,:)'); drawnow; pause;

            ind = [ind; spikes(ind_m)];
        %    r{m} = r_euclid;
    end
    cohenoptics = ind;
end